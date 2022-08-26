```bash
mvn clean package -P prodBJ
mvn clean package -P prodSG
```



## application.properties

```properties
spring.profiles.active=@spring.profiles.active@
```



## pom.xml:  list profiles

```xml
<profiles>
  <profile>
    <id>prodBJ</id>
    <properties>
      <spring.profiles.active>prodBJ</spring.profiles.active>
    </properties>
  </profile>
  <profile>
    <id>prodSG</id>
    <properties>
      <spring.profiles.active>prodSG</spring.profiles.active>
    </properties>
  </profile>
</profiles>
```



## Pom.xml: profile打包失败问题解决

```xml
<!--  profile打包失败 -->
<!-- https://blog.csdn.net/jiang_zf/article/details/86078732 -->
<!-- 主要是少了filtering这一行 -->
<resources> 
    <resource> 
    <directory>src/main/resources</directory>
    <filtering>true</filtering> 
    </resource> 
</resources>
```

