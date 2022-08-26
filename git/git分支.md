- 1 **创建**

​	`git branch develop`

- 2 **切换**  `HEAD指针，指向当前分支` `即改变HEAD指针指向`


​	`git checkout develop`

> 创建并自动切换到相应的分支
>
> `git checkout -b develop`

- 3 **合并** 切换回master，并将develop分支中的变更合并到master

```bash
git checkout master
git merge develop 
# Fast-forward模式，说明master分支没有提交新的更新，指针直接前移动即可
```

- 4 **冲突** 两个分支对同一行进行了修改

![image-20211229102804010](https://s2.loli.net/2021/12/29/s5p8SQUCaBLbdz6.png)

> - 查看所有冲突的文件
>
> ![image-20211229102953126](https://s2.loli.net/2021/12/29/ET71QY49yaxJMbv.png)
>
> - 打开查看冲突文件，会发现git生成的<<<<<<<,\=\=\=\==\=\=,>>>>>>>标记不同分支的变更
>
> ![image-20211229103612445](https://s2.loli.net/2021/12/29/xuGNRfSMUdHWzKX.png)
>
> - 手动删除git生成的多余字符，并保留其中一个版本的修改。
> - 然后，提交变更到暂存区，标记为已解决
>
> ```bash
> git add .
> git commit -am 'conflict'
> ```
>
> - 冲突较多时，用工具`TortoiseGit`

- 5 **远程** 

> - push命令将**本地分支**的信息推送到了远程仓库
> - 同时，如果远程没有同名分支，自动创建、并建立跟踪关系
>
> `git push origin master`
>
> - 查看远程仓库， `远程仓库/分支名称`
>
> `git branch -r`
>
> ![image-20211229104738445](https://s2.loli.net/2021/12/29/qDTsONketjzAVid.png)
>
> - 如果当前分支与远程分支存在跟踪关系，push/pull可省略分支名称
>
> ```bash
> git pull origin
> git push origin
> ```
>
> - 允许手动建立跟踪关系， 只要为branch命令添加**'-u'或'--set-upstream-to'** 即可
> - 如下，就能让**一个本地分支跟踪多个远程分支**
>
> `git branch -u origin/strick`
>
> - 如果当前分支只有一个可跟踪的远程分支，那么pull/push连仓库名都可以省略
>
> ```bash
> git pull
> git push
> ```

- 6 **删除** 本质上做的只是移除相应的指针

```bash
git branch -d develop  # 删除本地分支
git push origin --delete develop  # 删除远程分支
```

- 7 **变基（Rebase）** 将一条分支上的变更转移到另一条分支上

> - 如下，切换到develop分支，执行rebase命令
> - 变基目标是master而不是develop
>
> ![image-20211229110638899](https://s2.loli.net/2021/12/29/jLibZrNhXtBUFvA.png)
>
> ![image-20211229110650878](https://s2.loli.net/2021/12/29/diW7n8PLqHDBSjr.png)
>
> ![image-20211229111214190](https://s2.loli.net/2021/12/29/noqb1BCGvD4syth.png)
>
> - 发生冲突，需解决冲突后，先执行**add**命令， 注意，不需要~~commit~~命令
> - 然后，再为rebase命令添加`--continue`
>
> ```bash
> git add .
> git rebase --continue
> ```
>
> - 接着，切换回master分支，并将develop分支中的修改合并进来
>   - 注意，此时开启快进模式
>
> ```bash
> git checkout master
> git merge develop
> ```
>
> - 最后，执行log命令，就能看到develop分支所提交的版本添加到了master分支的后面
>   - 注意，它没有根据提交时间按顺序插入
>
> ![image-20211229112428552](https://s2.loli.net/2021/12/29/LCOyM3xwJujZorN.png)
>
> - 能将分叉的提交历史梳理成一条直线
> - 它会**改变提交历史**，这与合并完全不同
> - 变基还有一条基本原则，即只对尚未推送和分享给他人的**本地修改允许**执行变基操作

