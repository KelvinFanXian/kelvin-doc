# ref

- [子网掩码与ip地址有实际关系吗？](https://www.zhihu.com/question/29723388/answer/2111774926)



# B(error mask) ping A

- 两台服务器A和B的网络配置如下，B的子网掩码本应该是255.255.255.0，被不小心配成了255.255.255.224 。 它们不能正常通信吗？

![image-20220531185131602](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20220531185131602.png)



![image-20220606102401901](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20220606102401901.png)

![image-20220606141641402](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20220606141641402.png)



# ARP ip--->mac

- 地址解析协议，即ARP（**Address Resolution Protocol**），是根据[IP地址](https://baike.baidu.com/item/IP地址)获取[物理地址](https://baike.baidu.com/item/物理地址/2129)的一个[TCP/IP协议](https://baike.baidu.com/item/TCP%2FIP协议)

- 主机发送信息时将包含目标IP地址的ARP请求**广播**到局域网络上的所有主机，并接收返回消息，以此确定目标的物理地址；收到返回消息后将该IP地址和物理地址存入本机ARP缓存中并保留一定时间，下次请求时直接查询ARP缓存以节约资源。



### weaknesses

- 地址解析协议是建立在网络中各个主机互相信任的基础上的

- 局域网络上的主机可以自主发送ARP应答消息，其他主机收到应答报文时**不会检测该报文的真实性**就会将其记入**本机ARP缓存**

### Attack

由此攻击者就可以**向某一主机发送伪ARP应答报文**，使其发送的信息无法到达预期的主机或到达错误的主机，这就构成了一个[ARP欺骗](https://baike.baidu.com/item/ARP欺骗)。

[ARP命令](https://baike.baidu.com/item/ARP命令)可用于查询本机ARP缓存中IP地址和[MAC地址](https://baike.baidu.com/item/MAC地址)的对应关系、添加或删除静态对应关系等。相关协议有[RARP](https://baike.baidu.com/item/RARP)、[代理ARP](https://baike.baidu.com/item/代理ARP)。[NDP](https://baike.baidu.com/item/NDP)用于在[IPv6](https://baike.baidu.com/item/IPv6)中代替地址解析协议。



# RARP mac--->ip

- 反向地址转换协议（RARP：**Reverse Address Resolution Protocol**） 允许局域网的物理机器从[网关](https://baike.baidu.com/item/网关/98992)服务器的 ARP 表或者缓存上请求其 IP 地址。
- 网络管理员在局域网[网关](https://baike.baidu.com/item/网关/98992)[路由器](https://baike.baidu.com/item/路由器/108294)里创建一个表以映射[物理地址](https://baike.baidu.com/item/物理地址/2129)（MAC）和与其对应的 IP 地址。当设置一台新的机器时，其 RARP 客户机程序需要向[路由器](https://baike.baidu.com/item/路由器/108294)上的 RARP 服务器请求相应的 IP 地址。假设在[路由表](https://baike.baidu.com/item/路由表/2707408)中已经设置了一个记录，RARP 服务器将会返回 IP 地址给机器，此机器就会存储起来以便日后使用。 RARP 可以使用于[以太网](https://baike.baidu.com/item/以太网/99684)、[光纤分布式数据接口](https://baike.baidu.com/item/光纤分布式数据接口/3004891)及[令牌环](https://baike.baidu.com/item/令牌环/986104) LAN



# ICMP

ICMP（**Internet Control Message Protocol**）Internet控制[报文](https://baike.baidu.com/item/报文/3164352)协议。它是[TCP/IP协议簇](https://baike.baidu.com/item/TCP%2FIP协议簇)的一个子协议，用于在IP[主机](https://baike.baidu.com/item/主机/455151)、[路由](https://baike.baidu.com/item/路由)器之间传递控制消息。控制消息是指网络通不通、主机是否可达、[路由](https://baike.baidu.com/item/路由/363497)是否可用等网络本身的消息。这些控制消息虽然并不传输用户数据，但是对于用户数据的传递起着重要的作用。



# 子网掩码位数对照

| 128      | 192      | 224      | 240      | 248      | 252      | 254      | 255      |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
| 25       | 26       | 27       | 28       | 29       | 30       | 31       | 32       |
| 10000000 | 11000000 | 11100000 | 11110000 | 11111000 | 11111100 | 11111110 | 11111111 |

