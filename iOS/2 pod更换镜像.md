# pod更换镜像

```bash
cd ~/.cocoapods/repos
pod repo remove master
git clone https://mirrors.tuna.tsinghua.edu.cn/git/CocoaPods/Specs.git master

# git clone 下载很慢
## 首先你已翻墙，然后用下面两行命令映射到1080端口
# proxy
git config --global http.proxy socks5://127.0.0.1:1080
git config --global http.https://github.com.proxy socks5://127.0.0.1:1080
# to cancel proxy
git config --global --unset http.proxy
git config --global --unset http.https://github.com.proxy


# git clone 到最后会出现报错 error: RPC 失败。curl 92 HTTP/2 stream 3 was not closed cleanly before end of the underlying stream
git config --global http.version HTTP/1.1
git config --global http.postBuffer 524288000


#  删除pod自带的库
pod repo update
pod repo remove trunk


# 最后进入自己的工程，在自己工程的podFile第一行加上：
source 'https://mirrors.tuna.tsinghua.edu.cn/git/CocoaPods/Specs.git'

# 执行依赖安装
pod install

# 用xcode打开*.xcworkspace文件 （m1需要用Rosetta模式打开）
```









