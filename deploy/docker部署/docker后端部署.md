# dockerfile-prd

```dockerfile
FROM  base-jdk:1.8
MAINTAINER pisen
ENV PRO_PATH /home/scm
ENV TZ="Asia/Shanghai"
WORKDIR $PRO_PATH
# 创建目录
RUN mkdir -p $PRO_PATH
# 指定路径
WORKDIR $PRO_PATH
# 复制jar文件到路径
COPY scm-financial.jar $PRO_PATH/scm-financial.jar
COPY bootstrap.yml $PRO_PATH/bootstrap.yml
# 启动服务
ENTRYPOINT ["java","-jar","-Xmx2048m","/home/scm/scm-financial.jar","-Dfile.encoding=utf-8","--spring.config.location=/home/scm/bootstrap.yml"]
EXPOSE 19510

```



# deploy.sh

```bash
# 1. 上传新版本jar到 `/scm/cloud/scm-financial/scm-financial.jar`
# 2. 根据docker file构建镜像
`docker build -f dockerfile -t scm-financial:1.0 .`
# 3. 启动镜像为容器
`docker run -d 
-p 19510:19510 
-v $host_log_dir:$container_log_dir 
--name scm-financial scm-financial:1.0`
```



```bash
container_name="scm-financial";
container_port=19510
container_version=1.0
container_log_dir="/home/scm/logs"
host_log_dir="/scm/logs";

echo "start deploy ,$container_name:$container_version,port:$container_port"
function isNull(){
val=0
if [ ! -n "$1" ]; then
  val=0
else
  val=1
fi
return $val;
}

cd /scm/cloud/$container_name
#判断是dickerfile是否存在
if [ ! -f "dockerfile" ]; then
	echo "dockerfile not exist"
	exit
fi

function getRunningContainerId(){
containerId=`docker ps -f name=$container_name -q`
}

function getAllContainerId(){
containerId=`docker ps -f name=$container_name -a -q`
}

#获取正在运行容器ID
getRunningContainerId
echo "RunningContainerId:$containerId"
isNull "$containerId"
if (($?>0))
then
docker stop $containerId
fi

#获取容器ID
getAllContainerId
echo "AllContainerId:$containerId"
isNull "$containerId"
if (($?>0))
then
docker rm $containerId
fi


image_id=`docker images --filter=reference='$container_name:$container_version' --format "{{.ID}}"`
isNull "$image_id"
if (($?>0))
then
docker rmi $image_id
fi
#构建镜像
docker build -f dockerfile -t $container_name:$container_version .
docker run -d -p $container_port:$container_port -v $host_log_dir:$container_log_dir --name $container_name $container_name:$container_version

echo "finish deploy,$container_name:$container_version,port:$container_port"
```



# todo

```bash
docker build -f dockerfile -t admin-ui:1.0 .dang
```

# Remove none

```bash
docker rmi $(docker images -f "dangling=true" -q)
```

