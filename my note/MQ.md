# ref

# [消息队列经典十连问](https://juejin.cn/post/7067322260511522823)

# 10 让你写一个消息队列，该如何进行架构设计？

1 消息队列的整体流程

```bash
producer 发送消息给broker， broker存储好， broker再发送给consumer消费， consumer回复消费确认等
```

2 RPC如何设计

```bash
producer发送消息给broker， broker发消息给consumer消费，那就需要两次RPC了，RPC如何设计呢？
可以参考开源框架Dubbo，可以说说服务发现、序列化协议等等
```

3 broker考虑如何持久化

```bash
是放文件系统还是数据库呢，会不会消息堆积呢？消息堆积如何处理
```

4 消费关系如何保存

```bash
点对点还是广播方式呢？广播关系又是如何维护呢？zk还是config server
```

5 消费可靠性如何保证

```bash
如果消息重复了，如何幂等处理呢？
```

6 消息队列的高可用

```bash
可以参考Kafka的高可用保障机制。 多副本 --> leader & follower --> 挂了重新选举leader即可对外服务
```

7 消息事务特性

```bash
与本地业务同个事务， 本地消息落库； 消息投递到服务端，本地才删除； 定时任务扫描本地消息库，补偿发送
```

8 MQ的伸缩性和可扩展型

```bash
MQ的伸缩性和可扩展性， 如果消息积压或者资源不够时，如何支持快速扩容，提高吞吐？ 可以参照一下Kafka的设计理念， 
broker--> topic --> partition， 每个partition放一个机器， 就存一部分数据。

如果现在资源不够了， 简单啊， 给 topic增加partition，然后做数据迁移，增加机器，不就可以存放更多数据，提供更高的吞吐量
```



