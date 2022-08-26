# linux tab设置

- [vim设定Tab缩进长度](https://www.cnblogs.com/Cqlismy/p/11358259.html)

```sh
vim ~/.vimrc # 创建并设置
 
set number
set tabstop=4
set softtabstop=4
set shiftwidth=4
set noexpandtab
set autoindent
```



# vim格式化

```bash
# 设置当前的文件类型，这样vim才能根据文件类型来进行相应的格式化。
:set filetype=xml
# 因为缩进是默认关闭的，所以要手动打开。
:filetype indent on
# 上面修改了配置，:e重新加载文件。
:e
# =表示进行格式化，gg=G表示全文进行格式化。
gg=G
```

