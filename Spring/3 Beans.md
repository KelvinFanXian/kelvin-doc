## Beans

构成用户应用程序主干的对象



## 配置方式

- xml
- 注解  \<context:annotation-config/\>
- Java API
  - @Bean
  - @Configuration - 允许通过简单地调用同一个类中的其他 @Bean 方法 来定义 bean 间依赖关系。



## 集中bean scope (5种)

- Singleton - 每个 Spring IoC 容器仅有一个单实例。 
- Prototype - 每次请求都 会产生一个新的实例。
- Request - 每一次 HTTP 请求都会产生一个新的实例，并且该 bean 仅在当前 HTTP 请求内有效。
- Session - 每一次 HTTP 请求都会产生一个新的 bean， 同时该 bean 仅在当前 HTTP session 内有效。 
- Global-session - 类似于标准的 HTTP Session 作用域， 不过它仅仅在基于 **portlet** 的 web 应用中才有意义。 

Portlet 规范定义了全局 Session 的概念， 它被所有构成某个 portlet web 应用的各种不同的 portlet 所共享。 在 global session 作用域中定义的 bean 被限定于全局 portlet Session 的生命周期范 围内。 如果你在 web 中使用 global session 作用域来标识 bean， 那么 web 会自动当成 session 类型来使用。

仅当用户使用支持 Web 的 ApplicationContext 时， 最后三个才可用。



## spring bean 容器的生命周期

- 1、Spring 容器根据配置中的 bean 定义中实例化 bean。
- 2、Spring 使用依赖注入填充所有属性， 如 bean 中所定义的配置。 
- 3、如果 bean 实现 **BeanNameAware** 接口， 则工厂通过传递 bean 的 ID 来调用setBeanName()。 
- 4、如果 bean 实现 **BeanFactoryAware** 接口， 工厂通过传递自身的实例来调用 setBeanFactory()。 
- 5、如果存在与 bean 关联的任何 BeanPostProcessors， 则调用 preProcessBeforeInitialization() 方法。 
- 6、如 果为 bean 指定了 init 方法（ <bean> 的 init-method 属性）， 那么将调用它。 
- 7、最后， 如果存在与 bean 关联的任何 BeanPostProcessors， 则将调用postProcessAfterInitialization() 方法。 
- 8、如果 bean 实现 DisposableBean 接口， 当 spring 容器关闭时， 会调用 destory()。 
- 9、如果为 bean 指定了 destroy 方法（ <bean> 的 destroy-method 属性），那么将调用它。

![image-20211124181344499](https://i.loli.net/2021/11/24/qIFnC4mzHs59bea.png)



## spring 的内部 bean

内部bean通常是匿名的，它们的Scope一般是prototype



## 自动装配的不同模式

可以通过检查 BeanFactory 的内容让 Spring 自动解析 bean 的协作者。

- no - 这是默认设置， 表示没有自动装配。 应使用显式 bean 引用进行装配。 
- byName - 它根据 bean 的名称注入对象依赖项。 它匹配并装配其属性与 XML 文件中由相同名称定义的 bean。
- byType - 它根据类型注入对象依赖项。如果属 性的类型与 XML 文件中的一个 bean 名称匹配， 则匹配并装配属性。 
- 构造函数 - 它通过调用类的构造函数来注入依赖项。它有大量的参数。
- autodetect - 首先 容器尝试通过构造函数使用 autowire 装配，如果不能，则尝试通过 byType 自 动装配。