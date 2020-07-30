## quick revise
- ^ + h    del last character
- ^ + w    del last word
- ^ + u    del this line

## quick switch [insert/normal]
- ^ + c  or  ^ + [		instead ESC
- gi    last edit position

## move

> words

- w/W 	next word head
- e/ E      next word tail
- b/B      last word head

*Upper case: blank to split*

> line

- f{char} 	next char
  - ,		last char
  - ;        next char
- F{char}     last char

>  vertical

- ( )     sentence
- { }     paragraph

> page

- H/M/L     to head/middle/lower
- ^ + u / ^ + f     upward/forward
- zz     screen to central

## CURD

> Delete

- dw / diw 	delete word and blank / delete word only
- dt)     delete to )
- 3dd     delete 3 lines
- 3x     delete 3 chars

> Update

![image-20200523120526866](https://i.loli.net/2020/05/23/r41JWsZCYzmIOd8.png)

- ra     word --> aord
- 3ra      word --> aaad
- cw     delete word and switch to insertMode
- ct"     delete to " and switch to insertMode
- 3s      delete 3 char and switch to insertMode

> Read

- /word 	find word
  - n/N		next / last



###  Find and substitute

![image-20200523143305364](https://i.loli.net/2020/05/23/8gVejITaKUsboEY.png)

![image-20200523143353194](https://i.loli.net/2020/05/23/usovV9eAGdH8Ej7.png)

- :% s/self/this/g		all self --> this
- :1,6 s/self/this/g		1~6 row, self-->this
- :1,6 s/self//n		1~6 row, count self
- :% s/\\<quack\\>/jiao/g		accurate match



## multi file operation

![image-20200523144527710](https://i.loli.net/2020/05/23/3yr19MUYJsa2mfg.png)

![image-20200523144606629](/Users/xianfan/Library/Application Support/typora-user-images/image-20200523144606629.png)

![image-20200523144906813](https://i.loli.net/2020/05/23/KxBtCHy3ZmkAYTl.png)

![image-20200523144957011](https://i.loli.net/2020/05/23/XhjFIzql5Dm6bOx.png)

![image-20200523145223710](https://i.loli.net/2020/05/23/Dyn7E8WmQurgkAv.png)



![image-20200523145309240](https://i.loli.net/2020/05/23/3tCMz6PE1F8pgW9.png)

- ^ + W h     move this window to left

![image-20200523145742866](https://i.loli.net/2020/05/23/ANu8Wk7mD5Ee4hr.png)

![image-20200523145908665](https://i.loli.net/2020/05/23/n1WRFkc6U2BqCvw.png)

![image-20200523145956293](https://i.loli.net/2020/05/23/zuoSJKvaD1BiEMh.png)

![image-20200523150037502](https://i.loli.net/2020/05/23/U3F5NHiBuWKeJyT.png)



## Text Object

![image-20200523150532530](https://i.loli.net/2020/05/23/dEnfDqOeGv4ZMgF.png)

![image-20200523151139403](https://i.loli.net/2020/05/23/2vl4DL1Htcrn93J.png)

- ci{     delete chars in {} and switch insertMode



## Copy&paste and register

![image-20200523152829355](/Users/xianfan/Library/Application Support/typora-user-images/image-20200523152829355.png)

 ![image-20200523153451355](https://i.loli.net/2020/05/23/SBVTuq6iy2jY8bM.png)

![image-20200523153721736](https://i.loli.net/2020/05/23/Xuf2EjL7HgUlnPi.png)

- "+y      yank to clipboard
- "+p     put from clipboard



## macro

![image-20200523165359791](https://i.loli.net/2020/05/23/WEcqy4i9uZ2FQhf.png)



![image-20200523172429226](https://i.loli.net/2020/05/23/bn3s7DKBdJRl18c.png)

![image-20200523172520017](https://i.loli.net/2020/05/23/MWpNFeq9V2ciUtL.png)

##### add "" for each line

- qa     recording @a
  - add "" for a line
  - q     end recording
  - VG     select all
  - : normal @a      

- VG     select all
  - :normal I"     add " to line head
  - ^ + p     last command
  - :normal A"      add " to line end

## autoCompletion

![image-20200523174251547](https://i.loli.net/2020/05/23/x9yzZB5j34cDgOp.png)

![image-20200523174342774](https://i.loli.net/2020/05/23/KszwfNSRP1uZ4Dq.png)

