# 术语表

| #    | 简称        | 英文                                   | 说明 |
| ---- | ----------- | -------------------------------------- | ---- |
| 1    | **JTA**     | Java Transaction API                   |      |
| 2    | **JTS**规范 | Java Transaction Service Specification |      |
|      |             |                                        |      |



# 17.1 认识事务本身





# 17.2 事务家族成员

- Resource Manager
- Transaction Processing Monitor
  - Transaction Manager

- Applications

| #    | 成员                                                | 职责                                                         | 举例                                         |
| ---- | --------------------------------------------------- | ------------------------------------------------------------ | -------------------------------------------- |
| 1    | Resource Manager，**RM**                            | 存储并管理数据资源                                           | 数据库服务器、消息服务器                     |
| 2    | Transaction Processing Monitor，**TPM**，TP Monitor | 分布式事务场景中，协调多个RM处理事务                         | JTA&JTS中的应用服务器（Application Server）❓ |
| 3    | Transaction Manager，**TM**                         | TPM的核心模块，<br />1.协调多个RM处理事物<br />2.事务界定接口 Transaction Demarcation<br />3.事务上下文传播接口 Transaction Context Propagation）<br /> |                                              |
| 4    | Applications                                        | 独立形式存在或运行与容器中的应用程序，<br />事务边界的触发点 |                                              |



---

- 一个RM：局部事务Local Transaction
- 多个RM：全局事务Global Transaction / 分布式事务Distributed Transaction
  - 两阶段提交**协议**来保证整个事务的ACID，Two-Phase Commit，**2PC**


---



# 18.1 Java平台的局部事务支持

> 我们不是使用专用的事务API来管理事务，而是通过当前使用的
>
> **数据访问技术所提供的基于connection的API**来管理事务

## code18-1 基于JDBC API的事务管理代码示例

```java
Connection connection = null;
bean rollback = false;
try {
  connection = dataSource.getConnection();
  connection.setAutoCommit(false); -->
  // 使用JDBC进行数据访问...
  connection.commit(); <--
} catch (SQLException e) {
  rollback = true;
} finally {
  if (connection != null) {
    if(rollback) {
      try{
        connection.rollback(); // 🙋不需要关闭连接吗？
      } catch(SQLException e) {}
    }else{
      try {
        connection.close();
      } catch(SQLException e) {}
    }
  }
}
```



## code18-2 基于Hibernate API的事务管理

```java
Session session = null;
Transactioin transaction = null;
try {
  session = sessionFactory.openSession();
  transaction = session.beginTransaction(); -->
  // 使用Hibernate进行数据访问...
  session.flush();
  transaction.commit(); <--
} catch(HibernateException e) {
  transaction.rollback();
} finally {
  session.close();
}
 
'⚠️ 实际上，可以通过配置来决定：
  '让Hibernate使用 基于JDBC Connection的局部事务管理
  '还是让Hibernate使用 分布式的事务管理
```



## code18-3 JMS下的事务管理

- 通过JMS的**javax.jms.Session**来控制整个处理过程的事务

```java
boolean rollback = false;
Connection con = null;
Session session = null;
try {
  con = cf.createConnection();
  session = con.createSession(true, Session.AUTO_ACKNOWLEDGE);  -->
  // 使用JMS API处理响应消息
  session.commit(); <--
} catch (JMSException e) {
  rollback = true;
} finally {
  if(con != null) {
    if(rollback){
      try{
        session.rollback();
      } catch(JMSException e) {}
    } else {
      try{
        con.close();
      } catch(JMSException e) {}
    }
  }
}
```



# 18.2 Java平台的分布式事务支持

## 18.2.1 JTA (Java Transactioin API)



## 18.2.2 JCA (Java Connector Architecture)

```bash
可以不用因为将事务管理代码放在 DAO 层，而降低数据访问逻辑的重用性，
也可以在 Service 层根据相应逻辑，来决定提交或者回滚事务。

一般的 Service 对象可能需要在同一个业务方法中调用多个数据访问对象的方法，类似于图 19-1 这样的情况。

因为 JDBC 的局部事务控制是由同一个 java. Sql. Connection 来完成的，所以要保证两个 DAO 的数据访问方法处于一个事务中，我们就得保证它们使用的是同一个 java. Sql. Connection。要做到这一点，通常会采用称为 connection-passing 的方式，即为同一个事务中的各个 dao 的数据访问方法传递当
```





# 19.1 [事务]统一的过程

## 普通情况下的事务管理

```java
public void serviceMethod(){
  Object txObject=transactionManager.beginTransaction();
  ...
  dao1.daoDataAccess();
  dao2.daoDataAccess();
  ...
  transactionManager.commitTransaction(txObject);
}
```



## connection-passing方式的事务管理

```java
public void serviceMethod(){
  Object txObject=transactionManager.beginTransaction();
  Connection connection=(Connection)txObject;
  ...
  dao1.doDataAccess(connection);
  dao2.doDataAccess(connection);
  ...
  transactionManager.commitTransaction(txObject);
}


private Object beginTransaction(){
  Connection con=getConnection();
  con.setAutoCommit(false);
}
private void commitTransaction(Object txObject){
  Connection con=(Connection)txObject;
  con.commit();
  con.setAutoCommit(true);
}

// 问题：
'不但事务管理代码无法摆脱 java.Sql.Connection 的纠缠，而且数据访问对象的定义要绑定到具体的数据访问技术上来。
	'现在是使用 JDBC 进行数据访问，我们要在数据访问方法中声明对 java. Sql. Connection 的依赖，
	'那要是使用 Hibernate 的话，是不是又要声明对 Session 的依赖呢？显然，这样的做法是不可行的。
'不过好消息是，传递 Connection 的理念是对的，
  '只不过，在具体实施过程中，我们所采用的方法有些不对头。
// 改良方案：
'将java.Sql.Connection 绑定到 当前线程。 
  '绑定、提交、回滚、解绑

```



## TransactionResourceManager原型定义示例

```java
public class TransactionResourceManager{
  private static ThreadLocal resources = new ThreadLocal();
  
  public static Object getResource(){
    return resources.get();
  }
  public static void bindResource(Object resource) {
    resources.set(resource);
  }
  public static Object unbindResource(){
    Object res = getResource();
    resources.set(null);
    return res;
  }
}
```



# 19.2.1 TransactionDefinition

## 嵌套事务

```java
/* PROPAGATIOIN_REQUIRED */
A.service() {
  try{
    // PROPAGATIOIN_NESTED
    B.service();
  } catch(Exception e) {
    // PROPAGATIOIN_NESTED
    c.service
  }
}
```





# 19.2.3 PlatformTransactionManager

## 面向局部事务的PTM实现

- 数据访问技术与 PlatformTransactionManager 实现类对应关系

|数据访问技术|PlatformTransactionManager实现类|
| ---- | ---- |
|JDBC/iBATIS|DataSourceTransactionManager|
|Hibernate|HibernateTransactionManager|
|JDO|JdoTransactionManager|
|JPA  (Java Persistence API)|JpaTransactionManager|
|TopLink|TopLinkTransactionManager|
|JMS|JmsTransactionManager|
|JCA Local Transaction|CciLocalTransactionManager|



### 🤔why

```bash
提示如果我们的应用程序需要同时使用 Hibernate 以及 JDBC（或者 iBATIS）进行数据访问，那么可以使用 HibernateTransactionManager 对基于 Hibemate 和 JDBC（或者 iBATIS）的事务进行统一管理，
只要 Hibernate 的 SessionFactory 和 JDBC（或者 iBATIS）引用的是同一个 DataSource 就行。能猜到为什么吗？
```



## 面向全局事务的PTM实现

- Spring提供的支持分布式事务的PlatformTransac- tionManager实 现
  - `org.springframework.transaction.jta.JtaTransactionManager`

### 直接使用JTA规范接口进行分布式事务管理，面临的问题

- UserTransaction
  - 接口使用复杂，一长串异常需处理
  - 事务管理能力有限，对于事务的挂起`Suspend`/恢复`Resume`操作，只有JTA的Transaction才支持
- JTA规范没有明确要求对TransactionManager的支持
  - 各JTA提供商对TM实现差异

> 鉴于此，JtaTransactionManager对各种JTA实现提供的分布式事务支持进行了**统一封装**，
>
> 只不过它的所有的事务管理操作，最终都会**委派**给具体的JTA实现来完成。



```java
// 1.结合是否存在当前事务的情况，根据TransactionDefinition中指定的传播行为的不同语义执行后继承逻辑
// 2.根据情况挂起或恢复事务
// 3.提交事务之前检查[readOnly]字段是否被设置。如果是，以事务的回滚代替事务的提交
// 4.在事务回滚的情况下，清理并恢复事务状态
// 5.如果事务的[synchronization]处于[active]状态，在事务处理的规定时点触发注册的[Synchronization]回调接口。

public final TransactionStatus getTransaction(TransactionDefinition definition)throws TransactionException
public final void rollback(TransactionStatus status)throws TransactionException
public final void commit(TransactionStatus status)throw TransactionException
protected final SuspendedResourcesHolder suspend(Object transaction)throw TransactionException
protected final void resume(Object transaction, SuspendedResourcesHolder resourcesHolder)throws TransactionException
```



## getTransaction(TransactionDefinition definition)

```java
/*
- 主要目的：开启一个事务
	- 在此之前判断一下之前是否存在一个事务
		- 存在：根据TransactionDefinition中的传播行为决定是挂起当前事务/还是抛出异常
		- 不存在：也需要根据传播行为的具体语义来决定如何处理
		
1⃣️获取transaction object，判断是否存在当前事务
Object transaction = doGetTransaction();
	- 获取的transaction object类型会因具体实现类的不同各异，DataSourceTransactionManager会返回DataSourceTransactionManager.DataSourceTransactionObject类型实力，HibernateTransactionManager会返回HibernateTransactionManager.HibernateTransactionObject类型的实例，等等。 AbstractPlatformTransactionManager不需要知道具体实现类返回的transaction object具体类型是什么，因为最终对transaction object的依赖都将通过方法参数进行传递，只要具体的实现类取得transaction object参数后知道如何转型就行，所以，这一步返回的transaction为Object类型。
	- doGetTransaction()是getTransaction(TransactionDefinition)模板方法公开给子类来实现的abstract类型方法。DataSourceTransactionManager在实现doGetTransaction()方法逻辑的时候，会从TransactionSynchronizationManager获取绑定的资源，然后添加到DataSourceTransactioinObject之后返回。以此类推，其他AbstractPlatformTransactionManager子类都采用类似的逻辑实现了doGetTransaction()方法。
2⃣️获取Log类的debug信息，避免之后的代码重复。
3⃣️检查TransactionDefinition参数合法性。
*/
if (definition == null ) {
	// Use defaults if no transaction definition given.
	definition = new DefaultTransactionDefinition();
}
/*
如果definition参数为空，则创建一个DefaultTransactionDefinition实例以提供默认的事务定义数据。
4⃣️根据先前获得的transaction object判断是否存在当前事务，根据判定结果采取不同的处理方式
*/
if (isExistingTransaction(transaction)) {
  // Existing transaction found -> check propagation behavior to find out how to behave.
  return handleExistingTransaction(definition, transaction, debugEnabled);
}
/*
默认情况下，isExistingTransaction(transactoin)返回false，该方法需要具体子类根据情况进行覆写。对于DataSourceTransactionManager来说，它会根据传入的transaction所记载的信息进行判断：
*/
DataSourceTransactionObject txObject = (DataSourceTransactionObject) transation;
return (txObject.getConnectionHolder() != null && txObject.getConnectionHolder().isTransactionActive());

/*
	不管isExistingTransaction(transaction)返回结果如何，下面的处理主体上是以TransactionDefinition中的传播行为为中心进行的。比如同样是PROPAGATION_REQUIRED，在存在当前事务与不存在当前事务两种情况下的处理是不同的，前者会使用之前的事务，后者则会创建新的事务，其他的传播行为的处理也按照不同的场景分别处理。
*/
/*
	(a) 如果isExistingTransaction(transaction)方法返回true，即存在当前事务的情况下，由handleExistingTransaction()方法统一处理存在当前事务，应该如何创建事务对应的TransactionStatus实例并返回。

🏖️如果definition定义的传播行为是PROPAGATION_NEVER，抛出异常退出。如下：
*/
if (definition.getPropagationBehavior() == TransactionDefinition.PROPAGATION_NEVER) {
  throw new IllegalTransactionStateException(
  	"Existing transaction found for transaction marked with propagation 'never'");
}

/*
🏖️如果definition定义的传播行为是PROPAGATION_NOT_SUPPORTED，则挂起当前事务，然后返回，如下：
*/
if (definition.getPropagationBehavior() == TransactionDefinition.PROPAGATION_NOT_SUPPORTED) {
  if (debugEnabled) {
    logger.debug("Suspending current transation");
  }
  Object suspendedResources = suspend(transation);
  boolean newSynchronization = (getTransactionSynchronization() == SYNCHRONIZATION_ALWAYS);
  return newTransactionStatus(
  	definition, null, false, newSynchronization, debugEnabled, suspendedResources);
}

/*
newTransactionStatus()方法返回一个DefaultTransactionStatus实例，因为我们挂起了当前事务。而PROPAGATION_NOT_SUPPORTED不需要事务，所以，返回的DefaultTransactionStatus不包含transaction object的信息（构造方法第二个参数）。

🏖️如果definition定义的传播行为是PROPAGATION_REQUIREDS_NEW，则同样挂起当前事务，并开始一个新的事务并返回。如下：
*/
if (definition.getPropagationBehavior() == TransactionDefinition.PROPAGATION_REQUIRES_NEW) {
  if (debugEnabled) {
    logger.debug("Suspending current transaction, creating new transaction with name [" + definition.getName() + "]");
  }
  SuspendedResourcesHolder suspendedResources = suspend(transaction);
  try {
    doBegin(transaction, definition);
  } catch (TransactionException beginEx) {
    try {
      resume(transation, suspendedResources);
    } catch (TransactionException resumeEx) {
      logger.error(
        "Inner transaction begin exception overridden by outer transaction resume exception", beginEx);
      throw resumeEx;
    }
    throw beginEx;
  }
  boolean newSynchronization = (getTransactionSynchronization() != SYNCHRONIZATION_NEVER);
  return newTransactionStatus(
  	definition, transaction, true, newSynchronization, debugEnabled, suspendedResources);
}

/*
🏖️如果definition定义的传播行为是PROPAGATION_NESTED，根据情况创建嵌套事务，如通过Savepoint或者JTA的TransactionManager。如下：
*/
if (definition.getPropagationBehavior() == TransactionDefinition.PROPAGATION_NESTED) {
  if (!isNestedTransactionAllowed()) {
    throw new NestedtransactionNotSupportedException(
    	"Transaction manager does not allow nested transactions by default - " +
    	"specify 'nestedTransactionAllowed' property with value 'true'");
  }
  if (debugEnabled) {
    logger.debug("Creating nested transaction with name [" + definitioin.getName() + "]");
  }
  if (useSavepointForNestedTransaction()) {
    // Create savepoint within existing Spring-managed transaction,
    // through the SavepointManager API implemented by TransactionStatus.
    // Usually use JDBC 3.0 savepoints. Never activates Spring synchronization.
    DefaultTransactionStatus status = 
      newTransactionStatus(definition, transaction, false, false, debugEnabled, null);
    status.createAndHoldSavepoint();
    return status;
  } else {
    // Nested transaction through nested begin and commit/rollback calls.
    // Usually only for JTA: Spring synchronization might get activated here
    // in case of a pre-existing JTA transaction.
    doBegin(transaction, definition);
    boolean newSynchronization = (getTransactionSynchronization() != SYNCHRONIZATIOIN_NEVER);
    return newTransactionStatus(definition, trasaction, true, newSynchronization, debugEnabled, null);
  }
}

/*
🏖️当definition中定义的传播行为是PROPAGATION_REQUIRED、PROPAGATION_REQUIREDS_NEW或者PROPAGATION_NESTED的时候，开启新的事务。如下：
*/
if (definition.getPropagationBehavior() == TransactionDefinition.PROPAGATION_REQUIRED || definition.getPropagationBehavior() == TransactionDefinition.PROPAGATION_REQUIRES_NEW 
|| definition.getPropagationBehavior() == TransactionDefinition.PROPAGATION_NESTED) {
  SuspendedResourcesHolder suspendedResources = suspend(null);
  if (debugEnabled) {
    logger.debug("Creating new transaction with name [" + definition.getName() + "]:" + definition);
  }
  try {
    doBegin(transaction, definition);
  } catch(TransactionException ex){
    resume(null, suspendedResources);
    throw ex;
  }
  boolean newSynchronization = (getTransactionSynchronization() != SYNCHRONIZATION_NEVER);
  return newTransactionStatus(
  definition, transaction, true, newSynchronization, debugEnabled, suspendedResources);
}
/*
	之所以在doBegin之前先调用传入null的suspend()方法，是因为考虑到如果有注册的Synchronization，需要暂时将这些与将要开启的新事物无关的Synchronization先放一边。
剩下的其他情况，则返回不包含任何transaction object的TransactionStatus。这种情况下虽然是空的事务，但有可能需要处理在事务过程中相关的Synchronization。
*/
```

# rollback(TransactionStatus)

## 1 回滚事务

- 如果是嵌套事务，则通过TransactionStatus释放Savepoint
- 如果TransactionStatus表示当前事务是一个新的事务，则调用子类的doRollback(TransactionStatus)方法真正的回滚事务。
- 如果当前存在事务，并且rollbackOnly状态被设置，则调用由子类实现的doSetRollbackOlny(TransactionStatus)方法，各子类实现通常将当前的transaction object的状态设置为rollBackOlny。

## 2 触发Synchronization事件

- triggerBeforeComplemention(status)
- triggerAfterComplemention()

## 3 清理事务资源

- 设置TransactionStatus中的completed为完成状态
- 清理与当前事务相关的Synchronization
- 调用doCleanupAfterCompletion()释放事务资源，并解除到Transaction SynchronizationManager的资源绑定。  
  - 对于DataSourceTransactionManager来说，是关闭数据库连接，然后解除对DataSource对应资源的绑定。
- 如果之前有挂起的事务，恢复挂起的事务。





# code19-1 使用事务管理抽象API进行事务界定

```java
public class FooService{
  private PlatformTransactionManager transactionManager;
  
  public void serviceMethod() {
    TransactionDefinition definition = ...;
    TransactionStatus txStatus = getTransactionManager().getTransaction(definition);
    try {
      // dao1.doDataAccess();
      // dao2.doDataAccess();
      // ...
    } catch(DataAccessException e) {
      getTransactionManager().rollback(txStatus);
      throw e;
    } catch(OtherNecessaryException e) {
      getTransactionManager().rollback(txStatus);
      throw e;
    }
    getTransactionManager().commit(txStatus);
  }
  
  public PlatformTransactionManager getTransactionManager() {
    return transactionManager;
  }
  
  public void setTransactionManager(PlatformTransactionManager transactionManager) {
    this.transactionManager = transactionManager;
  }
}
```



# code19-2 TRM原型定义

```java
public class TransactionResourceManager {
  private static ThreadLocal resources = new ThreadLocal();
  
  public static Object getResource(){
    return resources.get();
  }
  public static void bindResource(Object resource) {
    resources.set(resource);
  }
  public static Object unbindResource(){
    Object res = getResource();
    resources.set(null);
    return res;
  }
}
```



# code19-3 面向JDBC的PlatformTM原型

```java
public class JdbcTransactionManager implements PlatformTransactionManager{
  private DataSource dataSource;
  
  public JdbcTransactionManager(DataSource dataSource){
    this.dataSource = dataSource;
  }
  
  public TransactionStatus getTransaction(TransactionDefinition definition) throws TransactionException {
    Connection connection;
    try{
      connection = dataSource.getConnection();
      TransactionResourceManager.bindResource(condition);
      return new 
        DefaultTransactionStatus(connection, true, true,false,true,null);
    } catch (SQLException e) {
      throw new 
        CannotCreateTransactionException("Can't get connection for tx", e);
    }
  }
  
  public void rollback(TransactionStatus txStatus) throws TransactionException{
    Connection connection = 
      (Connection) TransactionResourceManager.unbindResource();
    try {
      connction.rollback();
    }catch(SQLException e) {
      throw new 
        UnexpectedRollbackException("rollback failed with SQLException", e);
    } finally {
      try {
        connection.close();
      } catch (SQLException e) {
        // 记录异常信息，但通常不会有进一步有效的处理
      }
    }
  }
  
  public void commit(TransactionStatus txStatus) throws TransactionException{
    Connection connection = 
      (Connection) TransactionResourceManager.unbindResource();
    try{
      connection.commit();
    }catch(SQLException e) {
      throw new 
        TransactionSystemException("commit failed with SQLException", e);
    }finally {
      try {
        connection.close();
      }catch(SQLException e) {
        // 记录异常信息，但通常不会有进一步有效的处理
      }
    }
  }
}
```



# code19-4 嵌套事务

```java
/**
 * PROPAGATION_REQUIRED
 */
A.service() {
  try{
    // PROPAGATION_NESTED
    B.service();
  } catch(Exception e) {
    // PROPAGATION_NESTED
    c.service();
  }
}
```





# code19-5 事务提交

```java
boolean globalRollbackOnly = false;
if(status.isNewTransaction() || isFailEarlyOnGlobalRollbackOnly()) {
  globalRollbackOnly = status.isGlobalRollbackOnly();
}
...
if(globalRollbackOnly) {
  throw new UnexpectedRollbackException(
    "Transaction silently rolled back because it has been marked as rollback-only");
}

```

## suspend

把TransactionSynchronizationManager上当前事务对应的Synchronization信息以及资源获取到SuspendedResourcesHolder中，然后解除这些绑定

## resume

将SuspendedResourcesHolder中保持的信息重新绑定到TransactionSynchronizationManager



# AbstractPlatformTransactionManager

- doGetTransaction
- <u>isExistingTransaction</u>
- doBegin
- <u>doSuspend</u>
- doRollback
- <u>doSetRollbackOnly</u>
- <u>doResume</u>
- doCommit
- <u>doCleanupAfterCompletion</u>
- <u>shouldCommitOnGlobalRollbackOnly</u>
- <u>useSavepointForNestedTransaction</u>







# code20-1 PTM编程式事务管理

```java
DefaultTransactionDefinition definition = new DefaultTransactionDefinition();
definition.setTimeout(20);
...
TransactionStatus txStatus = transactionManager.getTransaction(definition);
try {
  // 业务逻辑实现
} catch(ApplicationException e) {
  transactionManager.rollback(txStatus);
  throw e;
} catch (Error e) {
  transactionManager.rollback(txStatus);
  throw e;
}
transactionManager.commit(txStatus);
```



# code20-2 TransactionTemplate

```java
TransactionTemplate txTemplate = ...;
Object result = txTemplate.execute(new TransactionCallback(){
  public Object doInTransaction(TransactionStatus txStatus) {
    Object result = null;
    // 各种事务操作
    return result;
  }});

// 或者

txTemplate.execute(new TransactionCallbackWithoutResult(){
  @Override
  protected void doInTransactionWithoutResult(TransactionStatus txStatus) {
  	// 事务操作1
    // 事务操作2
    // ...
  }});
```



# code20-3 抛出unchecked exception

```java
txTemplate.execute(new TransactionCallbackWithoutResult(){
  @Override
  protected void doInTransactionWithoutResult(TransactionStatus txStatus) {
    try{
      ...
    } catch(CheckedException e) {
      // 抛出特定的异常类型避免，一般情况下使用的RuntimeException
      throw new RuntimeException(e);
    }
  }});
```



# code20-4 设置rollBackOnly标志以使事务回滚

```java
txTemplate.execute(new TransactionCallbackWithoutResult(){
  @Override
  protected void doInTransactionWithoutResult(TransactionStatus txStatus){
    boolean needRollback = false;
    ...
    if(needRollback)
      txStatus.setRollbackOnly();
  }});
```



# code20-5 捕获checked exceptioin并回滚事务

```java
txTemplate.execute(new TransactionCallbackWithoutResultt(){
  @Override
  protected void doInTransactionWithoutResult(TransactionStatus txStatus) {
    try{
      ...
    } catch(CheckedException e) { //⚠️ swallow exception
      logger.warn("Transaction is Rolled back!", e);
    }
  }});
// TransactionTemplate，无法抛出任何checked exception
```



# code20-6 使用Savepoint创建嵌套事务

```java
txTemplate.execute(new TransactionCallbackWithoutResult(){
  @Override
  protected void doInTransactionWithoutResult(TransactionStatus txStatus) {
    BigDecimal transferAmount = new BigDecimal("20000");
    try{
      withdraw("WITHDRAW_ACCOUNT_ID", transferAmount); // 出金账户
      
      Object savePointBeforeDeposite = txStatus.createSavepoint();
      try{
        deposit("MAIN_ACCOUNT_ID", transferAmount); // 主入金账户
      } catch (DepositExceptioin ex) {
        logger.warn("rollback to savepoint for main acount transfer failure", ex);
        txStatus.rollbackToSavepoint(savePointBeforeDeposit);
        
        deposit("SECONDARY_ACCOUNT_ID", transferAmount); // 备入金账户
      } finally{
        // 最后一定要释放保存点
        txStatus.releaseSavepoint(savePointBeforeDeposit);
      }
    } catch(TransferException e) {
      logger.warn("failed to complete transfer operation!", e);
      txStatus.setRollbackOnly();
    }
  }
});
```



# code20-7 声明式事务管理-拦截器

```java
public class PrototypeTransactionInterceptor implements MethodInterceptor{
  private PlatformTransactionManager transactionManager;
  
  public Object invoke(MethodInvocation invocation) throws Throwable {
    Method method = invocation.getMethod();
    TransactionDefinition definition = getTransactionDefinitionByMethod(method);
    TransactionStatus txStatus = transactionManager.getTransaction(definition);
    Object result = null;
    try {
      result = invocation.proceed();
    } catch(Throwable t) {
      if(needRollbackOn(t)){
        transactionManager.rollback(txStatus);
      } else {
        transactionManager.commit(txStatus);
      }
      throw t;
    }
    transactionManager.commit(txStatus);
    return result;
  }
  protected boolean needRollbackOn(Throwable t) {
    // todo ...更多实现细节
    return false;
  }
  protected TransactionDefinition getTransactionDefinitionByMethod(Method method) {
    // TODO ...更多实现细节
    return null;
  }
  public PlatformTransactionManager getTransactionManager() {
    return transactionManager;
  }
  public void setTransactionManager(PlatformTransactionManager transactionManager) {
    this.transactionManager = transactionManager;
  }
}
```



# code20-8 QuoteService定义

```java
public class QuoteService implements IQuoteService {
  private JdbcTemplate jdbcTemplate;
  
  public Quote getQuate(){
    return (Quote)getJdbcTemplate().queryForObject("", new RowMapper(){
      public Object mapRow(ResultSet rs, int row) throws SQLException {
        Quote quote = new Quote();
        // ...
        return quote;
      }
    });
  }
  
  public Quote getQuateByDateTime(DateTime dateTime){
    throw new NotImplementedException();
  }
  public void saveQuote(Quote quote) {
    throw new NotImplementedException();
  }
  public void deleteQuote(Quote quote) {
    throw new NotImplementedException();
  }
  public JdbcTemplate getJdbcTemplate(){
    return jdbcTemplate;
  }
  public void setJdbcTemplate(JdbcTemplate jdbcTemplate){
    this.jdbcTemplate = jdbcTemplate;
  }
}
```



# code20-9 使用ProxyFactory和TransactionInterceptor进行事务管理

```xml
<beans>
	<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource" destory-method="close">
    <property name="url" value="jdbc:mydql://localhost/databaseName"/>
    <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
    <property name="username" value="..."/>
    <property name="password" value="..."/>
  </bean>
  
  <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
    <property name="dataSource" ref="dataSource"/>
  </bean>
  
  <bean id="transactionManager" class="org.springframework.transaction.interceptor.TransactionInterceptor">
  	<property name="transactionManager" ref="transactionManager"/>
    <property name="transactionAttributes">
    	<props>
      	<prop key="getQuate*">PROPAGATION_SUPPORTS, readOnly, timeout_20</prop>
        <prop key="saveQuote">PROPAGATION_REQUIRED</prop>
        <prop key="updateQuote">PROPAGATION_REQUIRED</prop>
        <prop key="deleteQuote">PROPAGATION_REQUIRED</prop>
      </props>
    </property>
  </bean>
  
  <bean id="quoteServiceTarget" class="...QuoteService">
    <property name="jdbcTemplate" ref="jdbcTemplate"/>
  </bean>
  
  <bean id="quoteService" class="org.springframework.aop.framework.ProxyFactoryBean">
    <property name="target", ref="quoteServiceTarget"></property>
    <property name="proxyInterfaces" vlaue="...IQuoteService"></property>
    <property name="interceptorNames">
    	<list>
      	<value>transactionInterceptor</value>
      </list>
    </property>
  </bean>
  
  <bean id="client" class="...QuoteServceClient">
    <property name="quoteService" ref="quoteService"/>
  </bean>
</beans>
```



# code20-10 TransactionInterceptor配置

```xml
<bean id="transactionInterceptor" class="org.springframework.transaction.interceptor.TransactionInterceptor">
  <property name="transactionManager" ref="transactionManager"></property>
  <property name="transactionAttributes">
  	<props>
    	<prop key="getQuate*">PROPAGATION_SUPPORTS,readOnly,timeout_20</prop>
      <prop key="saveQuote">PROPAGATION_REQUIRED</prop>
      <prop key="updateQuote">PROPAGATION_REQUIRED</prop>
      <prop key="deleteQuote">PROPAGATION_REQUIRED</prop>
    </props>
  </property>
</bean>
```



# code20-18 @Transactional声明式事务实现原型

```java
public Quote getQuate() {
  try {
    Method method = quoteService.getClass().getDeclaredMethod("getQuoate", null);
    bollean isTxAnnotationPresent = method.isAnnotationPresent(Transactional.calss);
    if(!isTxAnnotationPresent) {
      return (Quote)quoteService.getQuate();
    }
    Transactional txInfo = method.getAnnotation(Transactioinal.class);
    TransactionTemplate transactioinTemplate = new TransactionTemplate(transactionManager);
    if(!txInfo.propagation().equals(Propagation.REQUIRED))
      transactionTemplate.setPropagationBehavior(txInfo.propagation().value());
    if(txInfo.readOnly())
      transactionTemplate.setReadOnly(true);
    //...
    return (Quote) transactionTemplate.execute(new TrnasactionCallback(){
      public Object doInTransaction(TransactionStatus txStatus){
        return quoteService.getQuate();
      }
    });
  } catch(SecurityException e) {
    e.printStackTrace();
    return null;
  } catch(NoSuchMethodException e) {
    e.printStackTrace();
    return null;
  }
}
```



# 21.1 理解并活用ThreadLocal

# 21.2 谈Strategy模式在开发过程中的应用

- 在IoC容器根据bean定义的内容，实例化相应bean对象的时候，
  - 会根据情况决定使用**反射**还是使用**CGLIB** 来实例化相应的对象。 InstantiationStrategy是容器使用的实例化策略的抽象接口，
  - Spring框架默认提供了**CglibSubclassingInstantiationStrategy**和**SimpleInstantiationStrategy**两个具体实现类
- Spring的Validation框架中，org.springframework.validation.**Validator**定义也是一个策略接口，具体的实现类将根据**具体场景**提供不同的验证逻辑。



> Strategy模式通常宣称的可以避免**多重条件**语句的问题，其实只是将其**转移给客户端**代码而已



# 21.3 Spring 与JTA背后的奥秘

> JTA，Java Transaction API
>
> JCA即Java Connector Architecture，

```bash
'使用JtaTransactionManager进行分布式事务管理的时候，
	'要使用从应用服务器的JNDI服务获取的DataSource
	'而不是本地配置的普通dataSource'
```



|      | JTA                                  | Spring                |
| ---- | ------------------------------------ | --------------------- |
|      | javax.transaction.TransactionManager | JtaTransactionManager |
|      |                                      |                       |
|      |                                      |                       |

```bash
'JtaTransactionManager确实没有明确制定依赖于哪个资源，却依然能够将需要加入分布式事务的资源纳入其管理范围'

'JtaTransactionManager只是对各种具体的JTA实现产品提供的分布式事务管理功能进行了封装，
	'JtaTransactionManager会将最终的工作委派给具体的JTA实现来做
	
'具体的事务资源，RDBMS、MessageQueue等，要加入JTA管理的分布式事务，JTA规范要求其实现
'javax.transaction.xa.XAResource接口

'所以，希望加入JTA管理的分布式事务的RM通常会提供响应的适配器
	'用于提供基于XA Resource的分布式事务交互能力
		'比如关系数据库提供的支持XA的JDBC驱动程序，就是这样的适配器
		'这样，所有资源管理与事务交互的工作，基本上就由RM的适配器来统一管理来
```



```bash
# 之所以要求客户端应用程序通过应用服务器的JNDI获取DataSource等资源
'是因为只有使用 ApplicationSever公开的与XAResource绑定到同一 TransactionalResource的 Connection，
	'才可以保证客户端应用程序所做的所有数据访问操作能够 加入ApplicationServer所协调的分布式事务中
```



- ApplicationServer 为客户端应用程序 **公开** 与当前分布式事务相关的Connection的方式，
  - 就是实现一个DataSource，然后把该DataSource绑定到 **JNDI** 
    - 这样，客户端应用程序就可以通过从JNDI取得的DataSource中获取与事务相关的Connection了。
- 如果使用本地定义的DataSource，因为它与当前分布式事务不发生如何关系，所以也就根本不可能参与到分布式事务中去。



















