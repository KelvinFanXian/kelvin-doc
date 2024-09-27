# Name Interface

```java
Name objectName = new CompositeName("java:comp/env/jdbc");

Enumeration<String> elements = objectName.getAll();
while(elements.hasMoreElements()) {
  System.out.println(elements.nextElement());
}

objectName.add("example");

assertEquals("example", objectName.get(objectName.size() - 1));
```



# 2.2Context Interface

```java
SimpleNamingContextBuilder builder = new SimpleNamingContextBuilder();
builder.activate();

JndiTemplate jndiTemplate - new JndiTemplate();
ctx = (InitialContext) jndiTemplate.getContext();
```



# 3.JNDI Object Binding and Lookup

```java
ds = new DriverManagerDataSource("jdbc:h2:mem:mydb");

// 3.1 binding JNDI Objects
ctx.bind("java:comp/env/jdbc/datasource", ds);

// 3.2 Looking up JNDI Objects
DataSource ds = (DataSource) ctx.lookup("java:comp/env/jdbc/datasource");
assertNotNull(ds.getConnection());
```



# 4. Common JNDI Exception

```java
// 4.1 NameNotFoundException
ctx.lookup("badJndiName");

javax.naming.NameNotFoundException: Name [badJndiName] not bound; 0 bindings: []
  at org.springframework.mock.jndi.SimpleNamingContext.lookup(SimpleNamingContext.java:140)
  at java.naming/javax.naming.InitialContext.lookup(InitialContext.java:409)
    
// 4.2 NoInitalContextException

    
```



# 5. Role of JNDI in Modern Application Architecture

```bash
While JNDI plays less of a role in lightweight, containerized Java applications such as Spring Boot, there are other uses. Three Java technologies that still use JNDI are JDBC, EJB, and JMS. All have a wide array of uses across Java enterprise applications.

For example, a separate DevOps team may manage environment variables such as username and password for a sensitive database connection in all environments. A JNDI resource can be created in the web application container, with JNDI used as a layer of consistent abstraction that works in all environments.

This setup allows developers to create and control a local definition for development purposes while connecting to sensitive resources in a production environment through the same JNDI name.
```

