```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Inherited{
}
```

- @interface 	Inherited 实现了 java.lang.annotation.Annotation 接口

- @Inherited    它所标注的Annotation将具有继承性。

  - 被标注的类的子类调用 Son.class.isAnnotationPresent(Inheritable.class);  结果为true

  - Inheritable.java

    ```java
    @Target(ElementType.TYPE)
    @Retention(RetentionPolicy.RUNTIME)
    @Inherited
    @interface Inheritable{}
    ```

  - Father.java

    ```java
    @Inheritable
    class Father{
      public Father(){
        System.out.println("Father:" + 	Father.class.isAnnotationPresent(Inheritable.class));
      }
    }
    ```

    

