# 实现for循环的方式有三种：

1. 带循环list的for循环
2. 不带循环list的for循环 
3. C语法结果的for循环



## 1、带循环list的for循环

```bash
# 实现一：依次输出训练list中的内容 【list内容可以是数字也可以是串】

for item in 1 4 5  hello world
 do
    echo $item
done
运行结果：
1
4
6
hello
world


# 实现二：依次输出一个范围内的值，如下为输出1到5的值
for item in {1..5}
do
    echo $item
done
运行结果：
1
2
3
4
5

# 实现三： 设置输出的间隔值，如下为间隔2输出【也就是输出10以内的所有奇数】
for item in {1..10..2}
do
    echo $item
done
运行结果：
1
3
5
7
9

# 实现四： 输出当前目录下所有的文件和文件夹
for file in $(ls)
do
    echo $file
done

for file in *
do
    echo $file
done

```



## 2、不带循环list的for循环

> `【备注】 $@ 获取参数列表内容；  $# 获取输入参数的个数；`

```bash
# 将输入的参数循环输出【这里使用@来获取参数列表】，脚本test.sh 内容如下:

echo "input the world:"
for item in "$@"
do
    echo $item
done
echo "total param : $#"

运行： sh test.sh  hello world   【带了两个参数】
运行结果：
intput the world
hello
world
total param : 2

【备注】 $@ 获取参数列表内容；  $# 获取输入参数的个数；

```



## 二、新学到的方案：

```bash
seq 0 7 500
```



## 【关于seq指令】

seq命令用于输出 连续的数字、 固件间隔的数字、指定格式的数字； 具体示例如下：

```bash
# 一、输出连续的数字
   seq 1 100
     表示： 输出所有1到100之间的数字；

# 二、输出固定间隔的数字
   seq 0 7 500
     表示： 输出所有 0到500内 7个倍数；

# 三、输出指定格式的数字
   seq -s "+"  1 100
    表示：输出1到100之间的数字，每个数字间由+号间隔；
     
   seq -f "file%g"  1 10
    表示：输出给是为： file1  到 file10 ;  如下：
   file1
   file2
   file3
   file4
   file5
   file6
   file7
   file8
   file9
   file10


```

