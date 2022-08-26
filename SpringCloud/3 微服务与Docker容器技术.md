- 微服务运行所需要的容器技术
- 打造自动构建发布管道



# 1 Docker简介

- 完成一次环境配置后将其完整地迁移到其他计算机上使用
- 虚拟机缺点：
  - 创建复杂
  - 资源占用多
  - 启动慢
- Linux容器（Linux Containers，LXC）
  - 它不像虚拟机那样模拟运行一个完整的操作系统，而是**对进程进行隔离**
  - 容器启动相当于启动本机上的一个**进程**，而不是启动一个操作系统，启动速度非常快
  - 容器启动后才会占用所需要的资源，不会占用额外的资源，而且多个容器间的资源是可以共享的，虚拟机却只能独享
  - 由于容器仅是进程的集合，而且仅包含所需的组件即可，因此体积占用非常小
- Docker
  - Linux容器是属于系统底层的一种技术，而且使用起来也不方便，所以Docker就**基于Linux容器进行了封装**，并提供了简单易用的容器接口
  - 很方便地将软件与所需要依赖的库或组件打包整合到一个镜像文件里
    - 运行这个镜像，就生成了一个虚拟容器
  - 配置好的镜像文件可以很容易地通过Docker的接口进行
    - 备份导出，上传到仓库，从而达到复用的目的，而不需要每台计算机都重新配置一遍

## 优势

- 占用资源极少
- 更快的启动速度
- 更轻量的体积
- 更好的复用性





# 2 Docker 的使用

>  适用场景
>
> - 持续构建与持续交付
> - 更加自动、轻量、快捷的DevOps流程
> - **弹性云服务**的动态扩容与缩容
> - 搭建微服务架构
>
> 可以基于**Tomcat镜像**将WAR包复制到自定义构建的镜像中，然后直接在服务器中运行镜像即可

## 1. 安装

```bash
# to docker.com for centos
sudo yum install -y yum-utils
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo

sudo yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin

yum list docker-ce --showduplicates | sort -r

# sudo yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io docker-compose-plugin
sudo yum install docker-ce-20.10.9 docker-ce-cli-20.10.9 containerd.io docker-compose-plugin

sudo systemctl start docker

sudo docker run hello-world


docker
docker image --help
```

## 2. 镜像

> 仓库 - 镜像 - 容器

### 1 列出本机镜像

```bash
docker images
docker image inspect [IMAGE ID]
```



### 2 从仓库拉取镜像

```bash
docker pull NAME[:TAG]
docker pull openjdk:8u131-jre
```

### 3 Dockerfile文件，对镜像进行自定义配置

```dockerfile
# 基于openjdk的8u131-jre版本构建￼
FROM openjdk:8u131-jre￼
# 维护者￼
MAINTAINER microserv-usr￼
# Java环境变量￼
ENV JAVA_OPTS="-server -Xmx1G -Xms1G -Xmn200M -Duser.timezone=GMT+08"￼
# 容器运行执行命令￼
# 默认运行 /jar/app.jar￼
ENTRYPOINT [ "sh", "-c", "java $JAVA_OPTS -jar /jar/app.jar" ]
```

```bash
docker build -t microserv/openjdk:1.0.0 .
```

```dockerfile
FROM	#指定基于哪个基础镜像构建，这个指令是必须有的。

COPY	#将构建目录上下文中的文件或目录复制到镜像内。
ADD		#和COPY性质基本一致
# 但比COPY更高级，比如可以指定通过网络URL进行下载。
# 若从本地复制文件到镜像中，则推荐使用COPY。

ENV		#设置环境变量。
ARG		#效果和使用ENV命令的效果是一样的
# 区别是，ARG命令设置的环境在未来运行的容器中是不存在的。

EXPOSE	#暴露镜像所占用的端口，未来容器运行时所提供的服务端口。若容器中所运行的应用，如Tomcat有特定端口，建议都设置此参数，这样可以明确告诉使用者服务端口是哪个。
RUN			#执行命令行。此指令特别有用，尤其是镜像需要安装依赖、创建用户等操作，通过RUN可以很简单的向镜像内添加这些东西。但有一点需要注意，如果执行多个命令时，则尽量拼接在一层中去处理，如apt-get update && apt-get install nano。
WORKDIR	#指定工作目录。
USER	#指定容器运行所使用的用户。例如，运行Tomcat容器，可以将Tomcat指定为特定用户才允许运行。

CMD		#容器启动命令。若容器运行时需要启动某个脚本或应用，可以通过该参数进行设置，如打印字符串CMD [ "sh", "-c", "echo hello cmd" ]。
ENTRYPOINT	#和CMD目的一样，都是在指定容器中启动程序及参数。
# CMD的参数设置更像是指定了默认值，容器运行时可以覆盖参数值，
# ENTRYPOINT则表示容器运行时所需要的初始参数，这些参数不可被重写覆盖。

```





### 4 上传镜像

```bash
docker login
docker push microserv/openjdk:1.0.0

## 删除
docker rm [REPOSITORY|IMAGE ID] 	# 删除指定镜像
docker rmidocker images -aq				# 批量删除未被使用的镜像
# 技巧：有时下载镜像可能会遇到下载不完整而出现None这样的一些镜像，可以通过以下命令进行批量清理。
docker images --no-trunc| grep none | awk '{print $3}' | xargs -r docker rmi
```





## 3 容器

- 将应用程序复制到容器内运行
- 也可以通过**挂载宿主机**上的应用程序文件来运行

### 1 常见操作

``` bash
# 1 列出容器
docker ps

# 2 启动和重启容器
docker start [image...]
docker restart [image...]

# 3 停止和删除容器
docker stop [images...]
docker rm [images...]
##通常情况下，容器被删除后，除非内部文件被挂载到宿主机上，否则在容器内部修改或添加的东西都将会丢失。
##所以若使用容器运行比较重要的应用，如数据库，建议将相关目录挂载存储到宿主机的目录上

# 4 新建并运行容器
docker run [options]
## 指定后台运行模式、容器名称、数据卷挂载等
```

### 

## 4 容器实战 MySQL

```bash
# 设置UTF-8编码，支持emoji表情符号，支持东八区时区
docker pull mysql:5.7
```

- 创建mysql数据文件存储目录

<img src="https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20220705201018185.png" alt="image-20220705201018185" style="zoom:67%;" />

- my.cnf

```bash
# 设置UTF-8，避免查询乱码
[client]
default-character-set=utf8mb4
[mysql]
default-character-set=utf8mb4
[mysqld]
# 增加对emoji表情存储的支持
collation-server=utf8mb4_unicode_ci
character-set-server=utf8mb4
default_password_lifetime=0
# 将时区设置为东八区
default-time-zone='+8:00'
```





# 3 Docker 与Spring Cloud微服务

### 使用Linux Shell脚本的发布流程

1. ### 



# release-docker.sh

> - 发布脚本，运行Docker应用，需要上传到服务器。
> - 运行的容器是基于前面所构建的基础镜像microserv/openjdk:1.0.0
>   - 在这个镜像的Dockerfile文件中定义了**默认运行文件/jar/app.jar**。
>   - 因此我们需要将服务治理应用打包后的文件名称更改为app.jar
>   - 并通过-v参数将该文件挂载到容器中，
>   - 这样容器在启动时就可以直接运行服务治理应用。
> - 另外，在该脚本中将容器所暴露的端口设置为8260，也就是服务治理应用的端口。

```bash
#! /bin/bash￼
￼
# Docker容器基本信息￼
dockerName="servdiscovery"￼
dockerPort="8260"￼
# 挂载路径建议为绝对路径￼
dockerJarVolumn="/home/hucw/springcloud/${dockerName}"￼

# 停止删除现有容器￼
echo "停止删除容器：${dockerName}"￼
docker stop ${dockerName} && docker rm ${dockerName}￼

# Docker容器运行命令￼
echo ""￼
docker run --name=${dockerName} -d \￼
-p ${dockerPort}:${dockerPort} \￼
-v ${dockerJarVolumn}/jar:/jar \￼
microserv/openjdk:1.0.0￼

echo "${dockerName}容器已启动，使用命令查看启动日志：docker logs --tail=500￼ ${dockerName}"
```





# build.sh

> - 用于应用的编译打包，上传到服务器，最后执行发布脚本。

```bash
#! /bin/bash￼
￼
# 进入脚本所在目录，然后返回项目根目录￼
scriptPath="$( cd "$( dirname "${BASH_SOURCE[0]}" )" && pwd )"￼
cd $scriptPath￼
cd ..￼

# 服务器SSH配置￼
sshIP=yourserverIP￼
sshUsr=yourUser￼
sshPwd=yourpass￼
sshPort=22￼

# Docker容器基本信息￼
dockerName="servdiscovery"￼

# 编译打包￼
mvn clean package￼

# 复制JAR包到目录下￼
outputPath="${scriptPath}/${dockerName}";￼
if [ -d ${outputPath} ]; then￼
  rm -rf ${outputPath}￼
fi￼
mkdir ${outputPath} && mkdir ${outputPath}/jar￼

cp ./target/＊.jar ${outputPath}/jar/app.jar￼
cp ${scriptPath}/release-docker.sh ${outputPath}/release-docker.sh￼

# 删除服务器中已存在的目录￼
cd ${scriptPath}￼
appPath="~/springcloud"￼
ssh -p ${sshPort} ${sshUsr}@${sshIP} "[-d ${appPath}]||mkdir -p $￼{appPath}"￼
ssh -p ${sshPort} ${sshUsr}@${sshIP} "[-d ${appPath}/${dockerName}]&&￼rm -rf ${appPath}/${dockerName}"￼
ssh -p ${sshPort} ${sshUsr}@${sshIP} "[-d ${appPath}/${dockerName}]||￼mkdir -p ${appPath}/${dockerName}"￼

# 将整个目录上传到服务器上￼
scp -r -P ${sshPort} ./${dockerName} ${sshUsr}@${sshIP}:${appPath}￼

# 远程执行发布命令￼
ssh -p ${sshPort} ${sshUsr}@${sshIP} "sh ${appPath}/${dockerName}/release-￼docker.sh"
```

