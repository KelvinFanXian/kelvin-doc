```java
@Documented
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface MyAnnotation1{
}
```

1. @Interface
2. @Documented
3. @Target(ElementType.TYPE)
   1. 默认可以用在任何地方
4. @Retention(RetentionPolicy.RUNTIME)