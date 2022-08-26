- # Sed - stream editor

# sed的学习内容

![image-20220512094511018](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20220512094511018.png)

# sed-流处理编辑器

![image-20220512095030133](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20220512095030133.png)



# sed-行处理

![image-20220512095447684](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20220512095447684.png)



# sed-文本处理

![image-20220512095620042](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20220512095620042.png)

# sed-格式

![image-20220512095708401](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20220512095708401.png)

![image-20220512095808231](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20220512095808231.png)

# sed-举例

![image-20220512095915570](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20220512095915570.png)

# sed-p打印

![image-20220512100554287](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20220512100554287.png)

```bash
sed 'p' passwd  # 输出了两次
# sed原理？读入--处理--默认输出； 现在处理是打印p，那么就会打印2次

sed -n 'p' passwd # -n 抑制默认输出
```



# sed-行定位; (包含否定操作)

![image-20220512104550049](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20220512104550049.png)

![image-20220512104936698](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20220512104936698.png)

```bash
sed -n '10p' passwd # 输出第10行
nl passwd | sed -n '10p'
sed -n '/mooc/p' passwd

nl passwd|sed -n '10,20p' # 打印10~20行
nl passwd | sed -n '/root/, /nginx/p' passwd 

nl passwd | sed -n '10!p' # 不选择第10行
nl passwd | sed -n '10,20!p'  # 不选择10～20行

nl passwd | sed -n '1~2p' # 1行开始，步长为2
```



# sed-行处理

![image-20220512105508060](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20220512105508060.png)

```bash
# append
nl passwd | sed '5a ============'
nl passwd | sed '1,5a ============'

# insert
nl passwd | sed '5i ============'
nl passwd | sed '1,5i ============'

# change
nl passwd | sed '1c ever is root'
nl passwd | sed '1,5c ever is root' # 整段替换，而不是按行

# delete
nl passwd | sed '/root/d'

```



## 案例一：在配置文件中加入相应文本

![image-20220512110610495](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20220512110610495.png)

```bash
sed '$a port52113 \npermitRootLogin no' ssh_config  
#a增加，$最后一行

sed '$a \    port52113 \n    permitRootLogin no' ssh_config  
# 格式美化。\转义空格，因为a操作后面是语法空格，导致多个空格无效

```

##  mac 的表现不一样

```bash
#1.想写入原文件，需要 加 -i ''， 表示不做备份； 也可以做备份 -i 'xxx.bak'
#2.需要在a的后面加\，并且再手动回车一下
sed -i '' -e '$a\
appendline' test
```



## 案例二：文本处理

- 删除文本中空行

```bash
sed '/^$/d'
```



## 案例三：服务器日志处理

- 服务器log中找出error报错

```bash
sed -n '/Error/p' info.log
```



# sed-替换

![image-20220512142123535](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20220512142123535.png)

```bash
sed 's/false/true/' passwd
sed 's/:/%/g' passwd
```



## 案例四：数据筛选

- 获取eth0点ip

```bash
ifconfig
ifconfig ens33 | sed -n '/inet /p' | sed 's/inet.*r://' | sed 's/B.*$//'
# r:  匹配第一个冒号，抑制贪婪模式
```



# sed高阶-{}n

https://www.imooc.com/video/14489
