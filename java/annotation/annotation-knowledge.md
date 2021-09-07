## 可以标注

- 类
- 方法
- 变量
- 参数
- 包

## java定义了一套注解（共7个）

- 3个在java.lang
  - @Override 检查方法是否是重写方法。非重写方法会报错
  - @Deprecated
  - @SuppressWarnings
- 4个在java.lang.annotation （**元注解**：<u>注解的注解</u>）
  - @Retention 怎么保存
  - @Documented 是否包含在用户文档中
  - @Target 可以标记哪种Java成员
  - @Inherited 继承于哪个注解？

- **Java7** **Java8** 额外添加了3个注解
  - @SafeVarargs  - **java7** 忽略参数警告
  - @FunctionalInterface  - **java8**  标识匿名函数、函数式接口
  - @Repeatable - **java8** 标识某注解可以在同一个声明上使用多次



## Annotation组成部分

![image-20210128113556552](https://i.loli.net/2021/01/28/hxRTfDaSAK6GvcN.png)

1. **Annotation.java**

```java
package java.lang.annotation;
public interface Annotation{

    boolean equals(Object obj);

    int hashCode();

    String toString();

    Class<? extends Annotation> annotationType();
}
```

2. **ElementType.java**

```java
package java.lang.annotation;
public enum ElementType{
    TYPE, /*类、接口（包括注释类型）或枚举声明*/

    FIELD, /* 字段声明（包括枚举常量）*/

    METHOD,

    PARAMETER,

    CONSTRUCTOR,

    LOCAL_VARIABLE,

    ANNOTATION_TYPE,

    PACKAGE
}
```

3. **RetentionPolicy.java**

```java
package java.lang.annotation;
public enum RetentionPolicy{
  SOURCE, /*Annotation 信息仅存在于编译器处理期间，编译器处理完之后就没有该Annotation信息了*/
  
  CLASS, /* 编译器将Annotation存储于类对应的.class文件。默认行为*/
  
  RUNTIME /*编译器Annotation存储于class文件中，并且可由JVM读入*/
}
```



