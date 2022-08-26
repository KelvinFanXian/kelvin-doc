





#  Ubuntu安装Jenkins

1、创建部署目录

```
cd /home/openailab/
mkdir jenkins
```

2、下载Jenkins

```
cd jenkins
wget https://mirrors.tuna.tsinghua.edu.cn/jenkins/war/latest/jenkins.war
```

3、日志存放目录

```
mkdir logs
```

4、启动脚本设置

```
vim run_jenkins.sh
```

写入内容：

```
#!/bin/bash
export JENKINS_HOME=/home/openailab/jenkins
cd $JENKINS_HOME
nohup java -Dhudson.model.DownloadService.noSignatureCheck=true -Xmx2g -jar jenkins.war --httpPort=8080 > logs/jenkins.log 2>&1 &
tail -f logs/jenkins.log
```

5、创建配置文件

```
vim hudson.model.UpdateCenter.xml
```

写入内容：

```
<?xml version='1.1' encoding='UTF-8'?>
<sites>
        <site>
        <id>default</id>
        <url>https://updates.jenkins-zh.cn/update-center.json</url>
</site> 
```

6、运行jenkins

```
sudo chmod 777 run_jenkins.sh
sh run_jenkins.sh
```



## ref

- [Ubuntu安装Jenkins](https://www.icode9.com/content-3-1067449.html)