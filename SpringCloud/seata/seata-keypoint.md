## 第一个开启事务的需要添加seata全局事务注解

```java
@DS("order") // 每一层都需要使用多数据源注解切换所选择的数据库
@Override
@Transactional
@GlobalTransactional // 重点 第一个开启事务的需要添加seata全局事务注解
public void placeOrder(PlaceOrderRequest request)
{
```

## 事务传播特性设置为 REQUIRES_NEW 开启新的事务

```java
/**
 * 事务传播特性设置为 REQUIRES_NEW 开启新的事务 重要！！！！一定要使用REQUIRES_NEW
 */
@DS("product")
@Transactional(propagation = Propagation.REQUIRES_NEW)
@Override
public Double reduceStock(Long productId, Integer amount) {
    log.info("=============PRODUCT START=================");
    log.info("当前 XID: {}", RootContext.getXID());
```

```java
/**
 * 事务传播特性设置为 REQUIRES_NEW 开启新的事务 重要！！！！一定要使用REQUIRES_NEW
 */
@DS("account")
@Override
@Transactional(propagation = Propagation.REQUIRES_NEW)
public void reduceBalance(Long userId, Double price) {
    log.info("=============ACCOUNT START=================");
    log.info("当前 XID: {}", RootContext.getXID());
```





----



|             | Price | Stock |
| ----------- | ----- | ----- |
| **product** | 10    | 20    |

|             | Balance |
| ----------- | ------- |
| **Account** | 50      |



# 1 正常下单

|           | amount | Total_price |
| --------- | ------ | ----------- |
| **order** | 1      | 10          |

|             | Price | Stock |
| ----------- | ----- | ----- |
| **product** | 10    | 19    |

|             | Balance |
| ----------- | ------- |
| **Account** | 40      |

### 日志分析

```js
[begin,109]
[placeOrder,50]
[reduceStock,36]
[reduceBalance,37]
[suspend,188]
[commit,143]
[process,56]
[doBranchCommit,96]
[process,56]
[doBranchCommit,96]
[process,56]
[doBranchCommit,96]
```

```json
[begin,109] - Begin new global transaction [192.168.98.112:8091:8079739318321859573]
[placeOrder,50] - 当前 XID: 192.168.98.112:8091:8079739318321859573
[reduceStock,36] - 当前 XID: 192.168.98.112:8091:8079739318321859573
[reduceBalance,37] - 当前 XID: 192.168.98.112:8091:8079739318321859573
[suspend,188] - Suspending current transaction, xid = 192.168.98.112:8091:8079739318321859573
[commit,143] - [192.168.98.112:8091:8079739318321859573] commit status: Committed
[process,56] - rm client handle branch commit process:xid=192.168.98.112:8091:8079739318321859573,branchId=8079739318321859575,branchType=AT,resourceId=jdbc:mysql://localhost:3306/seata_product,applicationData=null
[doBranchCommit,96] - Branch committing: 192.168.98.112:8091:8079739318321859573 8079739318321859575 jdbc:mysql://localhost:3306/seata_product null
[process,56] - rm client handle branch commit process:xid=192.168.98.112:8091:8079739318321859573,branchId=8079739318321859577,branchType=AT,resourceId=jdbc:mysql://localhost:3306/seata_account,applicationData=null
[doBranchCommit,96] - Branch committing: 192.168.98.112:8091:8079739318321859573 8079739318321859577 jdbc:mysql://localhost:3306/seata_account null
[process,56] - rm client handle branch commit process:xid=192.168.98.112:8091:8079739318321859573,branchId=8079739318321859579,branchType=AT,resourceId=jdbc:mysql://localhost:3306/seata_order,applicationData=null
[doBranchCommit,96] - Branch committing: 192.168.98.112:8091:8079739318321859573 8079739318321859579 jdbc:mysql://localhost:3306/seata_order null
```





# 2 库存不足



|           | amount | Total_price |
| --------- | ------ | ----------- |
| **order** | 1      | 10          |

|             | Price | Stock |
| ----------- | ----- | ----- |
| **product** | 10    | 19    |

|             | Balance |
| ----------- | ------- |
| **Account** | 40      |

```json
// global_table  
{
    "xid": "192.168.98.112:8091:8079739318321859661",
    "transaction_id": 8079739318321859661,
    "status": 4,
    "application_id": "scm-financial",
    "transaction_service_group": "scm-financial-group",
    "transaction_name": "placeOrder(com.pisen.scm.financial.seata.domain.dto.PlaceOrderRequest)",
    "timeout": 60000,
    "begin_time": 1657184466679,
    "application_data": null,
    "gmt_create": "2022-07-07 17:01:06",
    "gmt_modified": "2022-07-07 17:01:06"
  }
```

### 日志分析

```javascript
[begin,109]
[placeOrder,50]
[reduceStock,36]
[suspend,188]
[rollback,178]
```

```json
[begin,109] - Begin new global transaction [192.168.98.112:8091:8079739318321859661]
[placeOrder,50] - 当前 XID: 192.168.98.112:8091:8079739318321859661
[reduceStock,36] - 当前 XID: 192.168.98.112:8091:8079739318321859661
[suspend,188] - Suspending current transaction, xid = 192.168.98.112:8091:8079739318321859661
[rollback,178] - [192.168.98.112:8091:8079739318321859661] rollback status: Rollbacked
```





# 3 用户余额不足

|           | amount | Total_price |
| --------- | ------ | ----------- |
| **order** | 1      | 10          |

|             | Price | Stock |
| ----------- | ----- | ----- |
| **product** | 10    | 19    |
|             | 10    | 13    |

```json
// product.undo_log 
{
    "id": 2,
    "branch_id": 8079739318321859797,
    "xid": "192.168.98.112:8091:8079739318321859795",
    "context": "serializer=kryo&compressorType=NONE",
    "rollback_info": "2D01AAB3A6A5C18680A1E01901010100696F2E73656174612E726D2E64617461736F757263652E756E646F2E53514C556E646F4C6FE7010101696F2E73656174612E726D2E64617461736F757263652E73716C2E7374727563742E5461626C655265636F7264F3011901010102696F2E73656174612E726D2E64617461736F757263652E73716C2E7374727563742E526FF7011901040103696F2E73656174612E726D2E64617461736F757263652E73716C2E7374727563742E4669656CE40101020169E408020201030101010170726963E5100A40240000000000000103010D0173746F63EB08021A0103010D016C6173745F7570646174655F74696DE5BA0101046A6176612E74696D652E4C6F63616C4461746554696DE5010100070007CC1F01110500040170726F647563F40101011901010102011901040103010A0B0802020103010D0E100A40240000000000000103010D100802260103010D12BA010104010100070007CC1F011038002E1601030170726F647563F4013139322E3136382E39382E3131323A383039313A383037393733393331383332313835393739B5",
    "log_status": 0,
    "log_created": "2022-07-07 17:05:05",
    "log_modified": "2022-07-07 17:05:05"
  }
```



|             | Balance |
| ----------- | ------- |
| **Account** | 40      |

```json
// branch_table
  {
    "branch_id": 8079739318321859797,
    "xid": "192.168.98.112:8091:8079739318321859795",
    "transaction_id": 8079739318321859795,
    "resource_group_id": null,
    "resource_id": "jdbc:mysql://localhost:3306/seata_product",
    "branch_type": "AT",
    "status": 0,
    "client_id": "scm-financial:192.168.98.112:49675",
    "application_data": null,
    "gmt_create": "2022-07-07 17:05:04.757120",
    "gmt_modified": "2022-07-07 17:05:04.757120"
  }

// global_table
  {
    "xid": "192.168.98.112:8091:8079739318321859795",
    "transaction_id": 8079739318321859795,
    "status": 5,
    "application_id": "scm-financial",
    "transaction_service_group": "scm-financial-group",
    "transaction_name": "placeOrder(com.pisen.scm.financial.seata.domain.dto.PlaceOrderRequest)",
    "timeout": 60000,
    "begin_time": 1657184704588,
    "application_data": null,
    "gmt_create": "2022-07-07 17:05:04",
    "gmt_modified": "2022-07-07 17:05:04"
  }

// lock_table
  {
    "row_key": "jdbc:mysql://localhost:3306/seata_product^^^product^^^1",
    "xid": "192.168.98.112:8091:8079739318321859795",
    "transaction_id": 8079739318321859795,
    "branch_id": 8079739318321859797,
    "resource_id": "jdbc:mysql://localhost:3306/seata_product",
    "table_name": "product",
    "pk": "1",
    "status": 1,
    "gmt_create": "2022-07-07 17:05:04",
    "gmt_modified": "2022-07-07 17:05:04"
  }
```

### 日志分析

```js
[begin,109]
[placeOrder,50]
[reduceStock,36]
[reduceBalance,37]
[process,56]
[doBranchRollback,123]
[info,43]
[suspend,188]
[rollback,178]
[warn,53]
[process,56]
[doBranchRollback,123]
[info,43]
[process,56]
[doBranchRollback,123]
[info,43]
[process,56]
[doBranchRollback,123]
```

```json
[begin,109] - Begin new global transaction [192.168.98.112:8091:8079739318321859795]
[placeOrder,50] - 当前 XID: 192.168.98.112:8091:8079739318321859795
[reduceStock,36] - 当前 XID: 192.168.98.112:8091:8079739318321859795
[reduceBalance,37] - 当前 XID: 192.168.98.112:8091:8079739318321859795
[process,56] - rm handle branch rollback process:xid=192.168.98.112:8091:8079739318321859795,branchId=8079739318321859797,branchType=AT,resourceId=jdbc:mysql://localhost:3306/seata_product,applicationData=null
[doBranchRollback,123] - Branch Rollbacking: 192.168.98.112:8091:8079739318321859795 8079739318321859797 jdbc:mysql://localhost:3306/seata_product
[info,43] - branchRollback failed. branchType:[AT], xid:[192.168.98.112:8091:8079739318321859795], branchId:[8079739318321859797], resourceId:[jdbc:mysql://localhost:3306/seata_product], applicationData:[null]. reason:[Branch session rollback failed and try again later xid = 192.168.98.112:8091:8079739318321859795 branchId = 8079739318321859797 Class cannot be created (missing no-arg constructor): java.time.LocalDateTime
[suspend,188] - Suspending current transaction, xid = 192.168.98.112:8091:8079739318321859795
[rollback,178] - [192.168.98.112:8091:8079739318321859795] rollback status: RollbackRetrying
[warn,53] - Retrying to rollback transaction[192.168.98.112:8091:8079739318321859795]
[process,56] - rm handle branch rollback process:xid=192.168.98.112:8091:8079739318321859795,branchId=8079739318321859797,branchType=AT,resourceId=jdbc:mysql://localhost:3306/seata_product,applicationData=null
[doBranchRollback,123] - Branch Rollbacking: 192.168.98.112:8091:8079739318321859795 8079739318321859797 jdbc:mysql://localhost:3306/seata_product
[info,43] - branchRollback failed. branchType:[AT], xid:[192.168.98.112:8091:8079739318321859795], branchId:[8079739318321859797], resourceId:[jdbc:mysql://localhost:3306/seata_product], applicationData:[null]. reason:[Branch session rollback failed and try again later xid = 192.168.98.112:8091:8079739318321859795 branchId = 8079739318321859797 Class cannot be created (missing no-arg constructor): java.time.LocalDateTime
[process,56] - rm handle branch rollback process:xid=192.168.98.112:8091:8079739318321859795,branchId=8079739318321859797,branchType=AT,resourceId=jdbc:mysql://localhost:3306/seata_product,applicationData=null
[doBranchRollback,123] - Branch Rollbacking: 192.168.98.112:8091:8079739318321859795 8079739318321859797 jdbc:mysql://localhost:3306/seata_product
[info,43] - branchRollback failed. branchType:[AT], xid:[192.168.98.112:8091:8079739318321859795], branchId:[8079739318321859797], resourceId:[jdbc:mysql://localhost:3306/seata_product], applicationData:[null]. reason:[Branch session rollback failed and try again later xid = 192.168.98.112:8091:8079739318321859795 branchId = 8079739318321859797 Class cannot be created (missing no-arg constructor): java.time.LocalDateTime
[process,56] - rm handle branch rollback process:xid=192.168.98.112:8091:8079739318321859795,branchId=8079739318321859797,branchType=AT,resourceId=jdbc:mysql://localhost:3306/seata_product,applicationData=null
[doBranchRollback,123] - Branch Rollbacking: 192.168.98.112:8091:8079739318321859795 8079739318321859797 jdbc:mysql://localhost:3306/seata_product
```





