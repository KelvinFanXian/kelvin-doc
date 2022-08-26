# Type id handling not implemented 

> 1、在nacos中修改seata的序列化方式，设置为kryo
>
> ```bash
> client.undo.logSerialization
> SEATA_GROUP
> kryo
> ```
>
> 
>
> 2、添加kryo以依赖
>
> - https://www.cnblogs.com/luckyang/p/14862630.html
> - https://www.jianshu.com/p/4e13d3c7a913



# com.mysql.jdbc.Driver和com.mysql.cj.jdbc.Driver

> 7.因为之前一直搞不明白com.mysql.jdbc.Driver和com.mysql.cj.jdbc.Driver的区别，于是去网上查了一下：
>
> com.mysql.jdbc.Driver 是 mysql-connector-java 5及5之前的，
>
> com.mysql.cj.jdbc.Driver 是 mysql-connector-java 6及6之后的的。
>
> 这里我的版本是5之后，所以加入了cj。问题解决了！！！根本原因就是mysql8.0与mysql-connector-java.jar版本一定要匹配！！！务必牢记！
>
> - https://www.cnblogs.com/iceywu/p/12292717.html



# 连接字

>      这个是重点： url: jdbc:mysql://192.168.56.20:3306/gulimall_admin?useSSL=false&autoReconnect=true&useUnicode=true&characterEncoding=UTF8&zeroDateTimeBehavior=convertToNull&allowMultiQueries=true
>
> - https://blog.csdn.net/qq_40996741/article/details/108160980



# seata换jar

> - https://blog.csdn.net/yanzhenjingfan/article/details/125472153



# 修改jar

> ```bash
> # 解压
> jar -xvf test.jar
> 
> # 压缩
> jar -cvf0m test.jar ./META-INF/MANIFEST.MF .
> ```
>
> - https://www.iszy.cc/posts/kmznu4/



# roll back 报错

[ref](https://yunyanchengyu.blog.csdn.net/article/details/124451955?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1-124451955-blog-116896511.pc_relevant_aa&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1-124451955-blog-116896511.pc_relevant_aa&utm_relevant_index=1)







# todo

- nacos
  - group的概念
  - 为什么添加 client.undo.logSerialization 就能够修改序列方式

