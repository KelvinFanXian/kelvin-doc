```java
@Target({TYPE, FIELD, METHOD, PARAMETER, CONSTRUCTOR, LOCAL_VARIABLE})
@Retention(RetentionPolicy.SOURCE)
public @interface SuppressWarnings {
    String[] value();
}
```

- String[] value(); 意味着，SuppressWarnings 能指定参数

- SuppressWarnings 的作用是，让编译器对"它所标注的内容"的某些警告保持静默。

  - @SuppressWarnings(value={"deprecation", "unchecked"})

    ```
    deprecation  -- 使用了不赞成使用的类或方法时的警告
    unchecked    -- 执行了未检查的转换时的警告，例如当使用集合时没有用泛型 (Generics) 来指定集合保存的类型。
    fallthrough  -- 当 Switch 程序块直接通往下一种情况而没有 Break 时的警告。
    path         -- 在类路径、源文件路径等中有不存在的路径时的警告。
    serial       -- 当在可序列化的类上缺少 serialVersionUID 定义时的警告。
    finally      -- 任何 finally 子句不能正常完成时的警告。
    all          -- 关于以上所有情况的警告。
    ```

