- [nowcoder](https://www.nowcoder.com/practice/205ccba30b264ae697a78f425f276779?tpId=195&tqId=36211&rp=1&ru=/exam/oj&qru=/exam/oj&sourceUrl=%2Fexam%2Foj%3Ftab%3DSHELL%25E7%25AF%2587%26topicId%3D195&difficulty=undefined&judgeStatus=undefined&tags=&title=)

# 1 文件行数

```bash
awk 'END{print NR}' nowcoder.txt
sed -n '$=' nowcoder.txt
cat nowcoder.txt |wc -l
```



# **2** **打印文件的最后5行**

```bash
tail -n 5
```



# 3 输出7的倍数

```bash
for num in {0..500..7}; do
    echo "${num}"
done
```

```bash
seq 0 7 500
```



# **4** **输出第5行的内容**

```bash
sed -n 5p
```



# **5** **打印空行的行号**

```bash
sed -n '/^$/=' nowcoder.txt
# = 行号
# mac行为不一致

awk '/^$/{print NR}' nowcoder.txt
# awk 和 sed 都是匹配行，然后执行操作


grep -n '^$' nowcoder.txt | awk -F: '{print $1}'
# grep 行号后有冒号(:)
```



# **6** **去掉空行**

```bash
sed '/^$/d' nowcoder.txt
```



# **7** **打印字母数小于8的单词**

```bash
# how they are implemented and applied in computer 

awk -F' ' '{for(i=1;i<=NF;i++){ if(length($i)<8)print $i }}' nowcoder.txt
# -F" "  用空格进行分割出字段
# NF number of fields in the current record,当前记录的字段数
# NR ordinal number of the current record
# $i 取i列的值
# $0 取整行
```



# **8** **统计所有进程占用内存大小的和**

```bash
awk '{sum+=6}END{print sum}' nowcoder.txt

# The special patterns BEGIN and END may be used to capture control before the first input line is read and after the last.  
# BEGIN and END do not combine with other patterns.  They may appear multiple times in a program and execute in the order they are read by awk.
```



# **9** **统计每个单词出现的个数**

```bash
awk '{for(i=1;i<=NF;i++)a[$i]+=1}END{for(x in a)print x, a[x]}' nowcoder.txt
# a['word']=1 索引数组
# print x, a[x] 逗号分隔打印多个值
# for(x in a) 其中x是索引值

```





# **10** **第二列是否有重复**

```bash
awk '{a[$2]+=1}END{ for(k in a){if(a[k]>1)print a[k],k} }' nowcoder.txt 
```



# **11** **转置文件的内容**

```bash
awk '{
    for(i=1;i<=NF;i++){
      if(NR==1){
        row[i] = $i;
      }else{
        row[i] = row[i]" "$i;
      }
    }
}END{
  for(i=1;i<=NF;i++){
    print row[i]
  }
}
' nowcoder.txt
```



# **12** **打印每一行出现的数字个数**

```bash
awk -F '[1,2,3,4,5]' 'BEGIN{sum=0}{print "line" NR " number:"(NF-1);sum+=(NF-1)}END{print "sum is " sum}' nowcoder.txt

# awk -F '[1,2,3,4,5]' 指定多个分隔符
# 不要忘记累加，同时用分号分隔;sum+=(NF-1)
```





# **13** **去掉所有包含this的句子**

```bash
grep -v 'this'
# -v, --invert-match

sed -n '/.*this.*/!p'
# !p 即不打印，否定操作

sed '/this/d'

awk '$0!~/this/ {print $0}'
# ~ (matches) or !~ (does not match)
# $0 整行
# $0!~/this/ 整行不匹配this
```



# **14** **求平均值**

```bash
awk '{if(NR==1){N=$1}else{sum+=$1}}END{printf("%.3f", sum/N)}'

```



# **15** **去掉不需要的单词**

```bash
sed '/.*b.*\|.*B.*/d'
# \| 表示或关系
awk '$0!~/B|b/ {print $0}'
# !~ 表不匹配
```



# **16** **判断输入的是否为IP地址**

```bash
# awk
awk -F "." '{
    if(NF == 4) {
        for(i=1;i<5;i++){
            if($i>255 || $i <0) {
                print("no");break
            }
        }
        if(i==5){print("yes")}
    } else {
        print("error")
    }
}'

# awk + exp
awk -F "." '{
    if (NF == 4) {
        if ($0 ~ /^((25[0-5]|2[0-4][0-9]|1[0-9][0-9]|[1-9][0-9]|[0-9])\.){3}(25[0-5]|2[0-4][0-9]|1[09][0-9]|[1-9][0-9]|[0-9])$/) {
                print("yes")
        } else {
                print("no")
        }
    } else {
        print("error")
    }
}'
```



# **17** **将字段逆序输出文件的每行**

```bash
awk -F ":" '{s="";for(i=NF;i>0;i--){s=s":"$i};print substr(s,2)}' nowcoder.txt
# 拼接 s=s":"$i
# substr(s,2)
```



# **18** **域名进行计数排序处理**

```bash
awk -F "/" '{a[$3]+=1}END{for(k in a){print a[k],k}}' nowcoder.txt | sort -r
# sort -r   对文本进行排序--反转
```



# **19** **打印等腰三角形**

```bash
for ((i=1;i<=5;i++))
do
  for ((j=5;j>i;j--))
	do
		echo -n " "
  done
  for ((k=1;k<=2*i-1;k++))
  do
  	if [ $(($k%2)) -eq 0 ];then
  		echo -n " "
  	else
  		echo -n "*"
  	fi
  done
  echo
done

# for 的后面一定加空格，否则报错：parse error near `)'
# echo -n 不换行
# if [ $(($k%2)) -eq 0 ];then
```



# **20** **打印只有一个数字的行**

```bash
awk '/(^[a-z]*[0-9][a-z]*$)/'
```





# **21** **格式化输出**

```bash
awk -F "" '
{
	for(i=1;i<=NF;i++){
		if((NF-i)%3==0&&i!=NF) printf $i",";
		else printf $i
	};
	printf "\n"
}
' nowcoder.txt
```



# **22** **处理文本**

```bash
awk -F ":" '
{ a[$1] = a[$1] $2 "\n" }
END{
	for (i in a) {
		printf("[%s]\n%s", i, a[i])
	}
}
' nowcoder.txt
```



# **23** **nginx日志分析1-IP统计**

```bash
grep '23/Apr/2020' nowcoder.txt | awk '{print $1}' | sort | uniq -c | sort -r | awk '{print $1, $2}'

# uniq -c 在前部显示出现次数
```





# **24** **nginx日志分析2-统计某个[时间段]的IP**

```bash
# awk - 利用map去重
awk '
{
    if ($0 ~ /\[23\/Apr\/2020:2[0-2]/) {
        a[$1] = 1
    }
}
END {
    print(length(a))
}
'

# grep
grep '23/Apr/2020' | cut -c '-12' | sort -u | wc -l
# cut -c '-12'  裁取前12位字符
# sort -u  去重并排序
# wc -l  文本行数，-l is line

# shell
declare -A map
while read line
    do
        a=($line)
        [[ ${a[3]} =~ 23/Apr/2020:2[0-2] ]] && ((map["${a[0]}"] = 0))
    done < nowcoder.txt
printf "${#map[*]}"
# a=($line) 这里=左右不能有空格
# declare -A map
# [[ ]] && (( ))
# "${#map[*]}"
# [[ ${a[3]} =~ 23/Apr/2020:2[0-2] ]] && ((map["${a[0]}"] = 0))   这句没看懂
```



# **25** **nginx日志分析3-统计访问3次以上的IP**

```bash
grep -Po '((25[0-5]|2[0-4]\d|[01]?\d\d?)\.){3}(25[0-5]|2[0-4]\d|[01]?\d\d?)' log.txt|sort|uniq -c|awk '{if($1>3){print $1,$2}}'|sort -r -n -k 1

# grep -P   grep用pear正则
# grep -o   grep只打印匹配的
# sort|uniq  uniq只能去重相邻行，需要和sort搭配使用
# uniq -c   uniq计算重复行数
# sort -r  倒序
# sort -n  按数字排序；默认按ascii排
# sort -k 1 指定按第一列排
# sort -t ' ' 按' '分割出列

```



# **26** **nginx日志分析4-查询某个IP的详细访问情况**

```bash
awk '$1 ~ /192.168.1.22/ {print $7}' log.txt | sort | uniq -c | sort -rn | sed 's/^ *//g'

# trim效果
# sed 's/^ *//g' 去掉左边的空格
# sed 's/ *$//g' 去掉右边的空格
```





# **27** **nginx日志分析5-统计爬虫抓取404的次数**

```bash
sed -n '/.*404.*baidu.*/p' nowcoder.txt | wc -l
```



# **28** **nginx日志分析6-统计每分钟的请求数**

```bash
awk -F "[ /]+" '{a[substr($6,6,5)]++}END{for(v in a)print a[v],v}' nowcoder.txt|sort -k1r

# sort -k1r -k2.1,2.2r
# sort -k1r
```



# **29** **netstat练习1-查看各个状态的连接数**

```bash
awk '
BEGIN{
	PROCINFO["sorted_in"]="@val_num_desc"
}
/tcp/{
a[$6]++
}
END{
	for(v in a)print v,a[v]
}' nowcoder.txt


#1
# PROCINFO
PROCINFO["sorted_in"]="@val_num_desc"}/tcp/{a[$6]++

#2
# @val_num_desc
# @x_y_z
## x：指定数组是要基于索引（ind）还是值（val）来排序。
## y：指定比较的方式。按字符串（str）比较，按数值（num）比较和按类型（type）比较。如果是按照类型，在升序的情况下，数值-->字符串-->数组。
## z：指定升序（asc）或者降序（desc）。
### @unsorted：无序。
### @ind_str_asc：基于索引按字符串比较方式升序排序。
### @ind_str_desc：基于索引按字符串比较方式降序排序。
### @ind_num_asc：基于索引按数值比较方式升序排序。无法转换成数值的一律视作数值0。
### @ind_num_desc：基于索引按数值比较方式降序排序。无法转换成数值的一律视作数值0。
### @val_type_asc：基于元素按照数据类型比较方式升序排序。
### @val_type_desc：基于元素按照数据类型比较方式降序排序。
### @val_str_asc：基于元素按照字符串比较方式升序排序。
### @val_str_desc：基于元素按照字符串比较方式降序排序。
### @val_num_asc：基于元素按照数值比较方式升序排序。
### @val_num_desc：基于元素按照数值比较方式降序排序。
```



# **30** **netstat练习2-查看和3306端口建立的连接**

```bash
awk '
BEGIN{
	PROCINFO["sorted_in"]="@val_num_desc"
}
$5 ~ /3306/ && $6 == "ESTABLISHED"{  
    sub(":3306","", $5);
    a[$5]++ 
}
END{
    for(x in a){ print a[x], x } 
}'

# sub(":3306","", $5);
## sub(r, t [, s])   r:regular, t:test, s:string
```



# **31** **netstat练习3-输出每个IP的连接数**

```bash
awk '
$1 ~ /tcp/{
  a[$5]++
}
END{
    for(i in a){ print i" "a[i] }
}
' | awk -F"[: ]" '{print $1" "$3}' | sort -nrk2
```



# **32** **netstat练习4-输出和3306端口建立连接总的各个状态的数目**

```bash
awk '
BEGIN{
	PROCINFO["sorted_in"]="@val_num_asc"
}
$5 ~ /3306/ {  
    ip_set[$5]=1;
    a[$6]++;
    cnt++;
}
END{
    print "TOTAL_IP", length(ip_set);
    for(x in a){ print x, a[x] };
    print "TOTAL_LINK", cnt
}' 
```



# **33** **业务分析-提取值**

```bash
awk '
BEGIN{FS="[:,]"}
/version/{print "serverVersion:" $NF}
/number/{print "serverName:" $NF}
/OS/{
	print "osName:" $(NF-2);
	print "osVersion:" $NF
}' nowcoder.txt

# BEGIN{FS="[:,]"}  以 :或, 进行分隔
```



# **34** **ps分析-统计VSZ,RSS各自总和**

```bash
awk '
{a+=$5;b+=$6}
END{print "MEM TOTAL\nVSZ_SUM:"a/1024"M,RSS_SUM:"b/1024"M"}
' nowcoder.txt

# 不用排出非数字
```

