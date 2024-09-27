# clash

```bash
#1
wget -c https://github.com/Dreamacro/clash/releases/download/v1.14.0/clash-linux-amd64-v3-v1.14.0.gz
#2
gzip -d clash-linux-amd64-v3-v1.14.0.gz && mv clash-linux-amd64-v3-v1.14.0.gz clash
#3
chmod +x clash 

## 
##
mkdir -p .config/clash
touch .config/clash/config.yaml

#4
curl 'https://sub.o4o.win/api/sub/f35c83f6a68418c808a5b0e18370cdb0?type=clash&ver=2' >$HOME/.config/clash/config.yaml

#5
./clash 
./clash >/dev/null 2>&1 < /dev/null &

# for git 
git config --global http.proxy socks5://127.0.0.1:7891
git config --global http.https://github.com.proxy socks5://127.0.0.1:7891
```





# profile 

```bash
# 每次打开bash都执行
~/.bashrc
/etc/bashrc   
# 只登录执行一次
~/.bash_profile
/etc/profile  
```



# which java

```bash
$ ls -l `which java`
lrwxrwxrwx 1 root root 22 Oct 19 11:49 /usr/bin/java -> /etc/alternatives/java
$ ls -l /etc/alternatives/java
lrwxrwxrwx 1 root root 35 Oct 19 11:49 /etc/alternatives/java -> /usr/lib/jvm/jre-1.5.0-gcj/bin/java
```



# jenv 

```bash
# install
$ git clone https://github.com/jenv/jenv.git ~/.jenv

# set variable
$ echo 'export PATH="$HOME/.jenv/bin:$PATH"' >> ~/.bash_profile
$ echo 'eval "$(jenv init -)"' >> ~/.bash_profile

# add
$ jenv add /usr/lib/jvm/java-8-openjdk-amd64
added
  
# list
$ jenv versions

# Configure global version
$ jenv global oracle64-1.6.0.39

# Configure local version (per directory)
$ jenv local oracle64-1.6.0.39

# Configure shell instance version
$ jenv shell oracle64-1.6.0.39



# if set by /root/.java-version
vim /root/.java-version
```



















