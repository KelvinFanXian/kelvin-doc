

## start

```bash
export JENKINS_HOME=/home/openailab/jenkins
cd $JENKINS_HOME
nohup java -Dhudson.model.DownloadService.noSignatureCheck=true -Xmx2g -jar jenkins.war --httpPort=8080 > logs/jenkins.log 2>&1 &
tail -f logs/jenkins.log
```



## stop

```bash
ps aux |grep jenkins.war |grep -v grep |awk '{print $2}' |xargs kill -9
```



## find

```bash
ps aux |grep PinShengErp-1.0-SNAPSHOT.jar
```

