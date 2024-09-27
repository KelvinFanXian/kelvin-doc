# todo

- [x] web访问
- [x] redis访问
- [x] 数据库连接池
- [x] mybatisPlus集成
  - [x] 数据自动填充：创建时间，创建人，修改人，修改时间
  - [ ] 代码生成？
- [ ] 访问控制
- [ ] 接口自动生成文档





# redis

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

```yaml
spring:
  redis:
    host: 192.168.6.154
    port: 6379
    database: 14
    password: 123456
```



# 数据库连接池

```xml
 <!-- 实现对数据库连接池的自动化配置 -->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
<dependency>
  <groupId>org.postgresql</groupId>
  <artifactId>postgresql</artifactId>
  <scope>runtime</scope>
</dependency>
```

```yaml
spring:
	datasource:
    url: jdbc:postgresql://192.168.6.154:5432/ym2fog
    username: postgres
    password: pisen@2024
    driver-class-name: org.postgresql.Driver
```



# MyBatis Plus

## config

```xml
<!-- 实现对 MyBatis Plus 的自动化配置 -->
<dependency>
  <groupId>com.baomidou</groupId>
  <artifactId>mybatis-plus-boot-starter</artifactId>
  <version>3.5.4</version>
</dependency>
```

```yaml
# mybatis-plus 配置内容
mybatis-plus:
  configuration:
    map-underscore-to-camel-case: true # 虽然默认为 true ，但是还是显示去指定下。
  global-config:
    db-config:
      id-type: ASSIGN_ID # 雪花算法id
      logic-delete-value: 1 # 逻辑已删除值(默认为 1)
      logic-not-delete-value: 0 # 逻辑未删除值(默认为 0)
  mapper-locations: classpath*:mapper/*.xml
  type-aliases-package: com.pisen.equipmentapi.dal.po

# logging
logging:
  level:
    # dao 开启 debug 模式 mybatis 输入 sql
    com:
      pisen:
        equipmentapi:
          dal:
            pg:
              mapper: debug
```

```java
@MapperScan("com.pisen.equipmentapi.dal.pg.mapper")
```

## PO & Mapper

```java
@Data
@TableName(value = "greeting")
public class GreetingPO {
    @TableId
    private Long id;
    private String content;
    private Long duration;
}
```

```java
public interface GreetingMapper extends BaseMapper<GreetingPO> {
}
```

## 自动填充创建/修改时间等

```java
package com.pisen.equipmentapi.framework.mybatis.core.handler;

import com.baomidou.mybatisplus.core.handlers.MetaObjectHandler;
import com.pisen.equipmentapi.framework.mybatis.core.dataobject.BaseDO;
import com.pisen.equipmentapi.framework.web.util.WebFrameworkUtils;
import org.apache.ibatis.reflection.MetaObject;

import java.time.LocalDateTime;
import java.util.Objects;

/**
 * 通用参数填充实现类
 *
 * 如果没有显式的对通用参数进行赋值，这里会对通用参数进行填充、赋值
 *
 * @author hexiaowu
 */
public class DefaultDBFieldHandler implements MetaObjectHandler {

    @Override
    public void insertFill(MetaObject metaObject) {
        if (Objects.nonNull(metaObject) && metaObject.getOriginalObject() instanceof BaseDO) {
            BaseDO baseDO = (BaseDO) metaObject.getOriginalObject();

            LocalDateTime current = LocalDateTime.now();
            // 创建时间为空，则以当前时间为插入时间
            if (Objects.isNull(baseDO.getCreateTime())) {
                baseDO.setCreateTime(current);
            }
            // 更新时间为空，则以当前时间为更新时间
            if (Objects.isNull(baseDO.getUpdateTime())) {
                baseDO.setUpdateTime(current);
            }

            Long userId = WebFrameworkUtils.getLoginUserId();
            // 当前登录用户不为空，创建人为空，则当前登录用户为创建人
            if (Objects.nonNull(userId) && Objects.isNull(baseDO.getCreator())) {
                baseDO.setCreator(userId.toString());
            }
            // 当前登录用户不为空，更新人为空，则当前登录用户为更新人
            if (Objects.nonNull(userId) && Objects.isNull(baseDO.getUpdater())) {
                baseDO.setUpdater(userId.toString());
            }
        } else { // 只有时间字段时
            Object createTime = getFieldValByName("createTime", metaObject);
            if (Objects.isNull(createTime)) {
                setFieldValByName("createTime", LocalDateTime.now(), metaObject);
            }
            Object modifyTime = getFieldValByName("updateTime", metaObject);
            if (Objects.isNull(modifyTime)) {
                setFieldValByName("updateTime", LocalDateTime.now(), metaObject);
            }
        }
    }

    @Override
    public void updateFill(MetaObject metaObject) {
        // 更新时间为空，则以当前时间为更新时间
        Object modifyTime = getFieldValByName("updateTime", metaObject);
        if (Objects.isNull(modifyTime)) {
            setFieldValByName("updateTime", LocalDateTime.now(), metaObject);
        }

        // 当前登录用户不为空，更新人为空，则当前登录用户为更新人
        Object modifier = getFieldValByName("updater", metaObject);
        Long userId = WebFrameworkUtils.getLoginUserId();
        if (Objects.nonNull(userId) && Objects.isNull(modifier)) {
            setFieldValByName("updater", userId.toString(), metaObject);
        }
    }
}
```

```java
@Bean
public MetaObjectHandler defaultMetaObjectHandler(){
    return new DefaultDBFieldHandler(); // 自动填充参数类
}
```





# 编码辅助

```xml
<!-- 编码辅助工具 -->
<dependency>
  <groupId>org.projectlombok</groupId>
  <artifactId>lombok</artifactId>
  <version>1.18.30</version>
</dependency>
<dependency>
  <groupId>cn.hutool</groupId>
  <artifactId>hutool-all</artifactId>
  <version>5.8.22</version>
</dependency>
```



# 日期类型处理

```java
@Configuration
public class LocalDateTimeSerializerConfig {
    @Value("${spring.jackson.date-format:yyyy-MM-dd HH:mm:ss}")
    private String pattern;

    @Bean
    public LocalDateTimeSerializer localDateTimeDeserializer() {
        return new LocalDateTimeSerializer(DateTimeFormatter.ofPattern(pattern));
    }

    @Bean
    public Jackson2ObjectMapperBuilderCustomizer jackson2ObjectMapperBuilderCustomizer() {
        return builder -> builder.serializerByType(LocalDateTime.class, localDateTimeDeserializer());
    }
}
```







# read

- [Spring-Boot](https://www.iocoder.cn/Spring-Boot/)

- ###### [芋道 Spring Boot MyBatis 入门](https://www.iocoder.cn/Spring-Boot/MyBatis/)

