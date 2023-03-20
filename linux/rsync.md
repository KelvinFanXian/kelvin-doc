## rsync默认写法

```bash
rsync -avz --delete ./dist/ root@124.222.8.90:/tmp/test
```

## ssh免密

```bash
# 生成公钥、私钥
ssh-keygen -t rsa
# 将公钥传输到远程主机
ssh-copy-id -i id_rsa.pub root@
```



## rsync借助ssh免密通道

```bash
rsync -avzP --delete ./dist/ -e 'ssh -p22' root@124.222.8.90:/tmp/test
```

> - -avz相当于-vzrtopgDl，表示同步时文件和目录属性不变。参数细节见后文。
> - -P显示同步的过程，可以用–progress 替换。
> - -e ‘ssh -p22’，表示通过ssh的**通道传输数据**，当远程主机ssh端口为22时，那麽-p22 可省略。





## ref

- https://www.cnblogs.com/shihuc/p/5628893.html

- http://blog.chinaunix.net/uid-20384937-id-1953760.html
- https://www.jianshu.com/p/50dff41a8766
- [ssh免密码登录](https://cloud.tencent.com/developer/article/1456064)

