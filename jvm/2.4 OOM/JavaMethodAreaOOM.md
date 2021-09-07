```bash
java -XX:PermSize=10M -XX:MaxPermSize=10M JavaMethodAreaOOM
```

```bash
Caused by: java.lang.OutOfMemoryError: PermGen space
```

`运行时产生大量的类去填满方法区，直到溢出。`

J2SE API 也能动态产生类：

- 反射时的GeneratedConstructorAccessor
- 动态代理

但是麻烦，这里借助`CGLib`使方法区出现OOM

---

- CGLib开源项目：http://cglib.sourceforge.net/

- `方法区`用于存放Class的相关信息：
  - 类名
  - 访问修饰符
  - 常量池
  - 字段描述
  - 方法描述

---

当前，Spring、Hibernate，在对类进行增强时，都会使用到CGLib这类字节码技术，增强的类越多，就需要**越大的方法区**来保证动态生成的Class可以加载入内存。

随着Groovy这样的动态语言（持续创建类来实现语言的动态性）的流行，也越容易遇到方法区溢出的情况。

---

类的回收条件苛刻，需要注意**类回收**的场景：

- Groovy等动态语言
- 使用了CGLib增强
- 大量JSP或动态产生JSP文件的应用
- 基于OSGi的应用（同一个类文件，被不同的加载器加载也会视为不同的类）