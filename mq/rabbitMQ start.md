- [超详细的RabbitMQ入门，看这篇就够了](https://zhuanlan.zhihu.com/p/163790007)

## 为什么使用MQ

- 解耦
- 异步
- 削峰

## **RabbitMQ的特点**

>- 可靠性。支持持久化，传输确认，发布确认等保证了MQ的可靠性。
>- 灵活的分发消息策略。这应该是RabbitMQ的一大特点。在消息进入MQ前由Exchange(交换机)进行路由消息。分发消息策略有：
>  - 简单模式、
>  - 工作队列模式、
>  - 发布订阅模式、
>  - 路由模式、
>  - 通配符模式。
>- 支持集群。多台RabbitMQ服务器可以**组成一个集群**，形成一个**逻辑Broker**。
>- 多种协议。RabbitMQ支持多种消息队列协议，比如 STOMP、MQTT 等等。
>- 支持多种语言客户端。RabbitMQ几乎支持所有常用编程语言，包括 Java、.NET、Ruby 等等。
>- 可视化管理界面。RabbitMQ提供了一个易用的用户界面，使得用户可以监控和管理消息 Broker。
>- 插件机制。RabbitMQ提供了许多插件，可以通过插件进行扩展，也可以编写自己的插件。

## 生产者-处理无队列问题

```java
//实现BeanPostProcessor类，使用Bean的生命周期函数
@Component
public class DirectRabbitConfig implements BeanPostProcessor {
    //这是创建交换机和队列用的rabbitAdmin对象
    @Resource
    private RabbitAdmin rabbitAdmin;
    
    //初始化rabbitAdmin对象
    @Bean
    public RabbitAdmin rabbitAdmin(ConnectionFactory connectionFactory) {
        RabbitAdmin rabbitAdmin = new RabbitAdmin(connectionFactory);
        // 只有设置为 true，spring 才会加载 RabbitAdmin 这个类
        rabbitAdmin.setAutoStartup(true);
        return rabbitAdmin;
    }
    
    //实例化bean后，也就是Bean的后置处理器
    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        //创建交换机
        rabbitAdmin.declareExchange(rabbitmqDemoDirectExchange());
        //创建队列, 防止无队列异常
        rabbitAdmin.declareQueue(rabbitmqDemoDirectQueue());
        return null;
    }
}
```

## 消费者-处理无队列问题

```java
@Component
//使用queuesToDeclare属性，如果不存在则会创建队列
@RabbitListener(queuesToDeclare = @Queue(RabbitMQConfig.RABBITMQ_DEMO_TOPIC))
public class RabbitDemoConsumer {
    //...省略
}
```

## **RabbitMQ中的组成部分**

- Broker：消息队列服务进程。此进程包括两个部分：Exchange和Queue。
- Exchange：消息队列交换机。**按一定的规则将消息路由转发到某个队列**。
- Queue：消息队列，存储消息的队列。
- Producer：消息生产者。生产方客户端将消息同交换机路由发送到队列中。
- Consumer：消息消费者。消费队列中存储的消息。

![img](https://pic3.zhimg.com/80/v2-2a75ca674c5fb43ce136bae5496b1c4e_1440w.jpg)

> - 消息生产者连接到RabbitMQ Broker，创建connection，开启channel。
> - 生产者声明交换机类型、名称、是否持久化等。
> - 生产者发送消息，并指定消息是否持久化等属性和routing key。
> - exchange收到消息之后，**根据routing key路由到跟当前交换机绑定的相匹配的队列**里面。
> - 消费者监听接收到消息之后开始业务处理。

## **Exchange的四种类型以及用法**