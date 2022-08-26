| cmd  | demo               | comments                |
| ---- | ------------------ | ----------------------- |
| ps   | ps -ef\|grep nginx |                         |
| lsof | lsof -i:8080       |                         |
| df   | Df -h              | display free disk space |



# find&grep

- find找文件
- grep找内容

## find

![image-20220506123756856](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20220506123756856.png)

![image-20220506123826322](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20220506123826322.png)

```bash
find . -name 'test*'
find . -type f -name 'test'
find . -mtime -1
find . -type f -mtime -1
find . -type d -empty -exec touch {}/.gitkeep \;
find . -name '.gitkeep' | xargs rm -rf

```



## grep

```bash
ps aux | grep java
grep -rn hello *

grep -C 2 '^core id' /proc/cpuinfo

```

| 基本正则                                                     | 扩展正则                                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![image-20220506141848704](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20220506141848704.png) | ![image-20220506142836321](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20220506142836321.png) |



# tar

|             | .tar                                                      | .tar.gz(z)                 | .tar.bz2(j)                 | .tar.xz(J)                | auto(a)   |
| ----------- | --------------------------------------------------------- | -------------------------- | --------------------------- | ------------------------- | --------- |
| 创建c       | tar -cf test.tar test                                     | tar -czvf test.tar.gz test | tar -cjvf test.tar.bz2 test | tar -cJvf test.tar.bz2 xz | tar -cavf |
| 解包x       | tar -xvf test.tar<br />tar -xvf test.tar -C targetFolder  | tar -zxvf test.tar.gz      | tar -jxvf test.tar.bz2      | tar -Jxvf test.tar.xz     | tar -xavf |
| 添加文件r   | tar -rvf test.tar file2<br />tar -rvf test.tar @file2.tar |                            |                             |                           |           |
| 查看包文件t | tar -tvf test.tar                                         |                            |                             |                           |           |





# less & more



# **nl** – line numbering filter



# seq

```bash
seq 0 7 500
```



# 查看开放的端口(待整理)

```bash
# 1.例如查看80端口是否开放，输入 lsof -i:端口号 ，如果没有信息出现则说明该端口还未开放
$ lsof -i:80
$ telnet 192.168.0.1 80

# 2.查看所有开放的端口
$ netstat -aptn
$ iptables -nL

# 3.以上只是查看端口的开放情况，以下将实现开启端口的命令，分别是查看防火墙状态、开启防火墙、开启端口、防火墙重启
$ ufw status
$ ufw enable
$ ufw allow 22
$ ufw reload
```

