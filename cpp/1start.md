# 1.1 编写一个简单的C++程序

- Function = return type + function name + parameter list + function body
- 类型 

## 1.1.1 编译、运行程序

````bash
vim test.cpp
```cpp
#include <iostream>

int main(){
    std::cout << "Hello, World!" << std::endl;
    return -1;
}
```


# 运行GUN编译器
g++ -o test test.cpp

# 查看./test 的返回值
echo $?
````



# 1.2 初识输入输出

## 标准输入输出对象

- stream - 随着时间的推移，字符是顺序生成或消耗的
- cin:istream - stantdard input
- cout:ostream - standard output
- cerr/clog:ostream - standard error



## 一个使用IO库的程序

```c++
// vim add.cpp
#include <iostream>
int main() {
    std::cout << "Enter two numbers:" << std::endl;
    int v1 =0, v2=0;
    std::cin>>v1>>v2;
    std::cout<<"The sum of " << v1 << " and " << v2 << " is " << v1+v2 << std::endl;
    return 0;
}
```



## 向流写入数据

表达式 = 运算符 + 运算对象

| 表达式                     | 运算符 | 运算对象                        |
| -------------------------- | ------ | ------------------------------- |
| cout<<"Enter two numbers:" | <<     | 👈：ostream对象  👉：要打印的内容 |

- std::cout << "Enter two numbers:" << std::endl;
  - << 运算符的返回起左侧运算对象，所以可以连续使用运算符
- << std::endl;
  - 操纵符（manipulator）- 结束当前行，并将与设备关联的缓冲区（buffer）中的内容刷到设备中



## 使用标准库中的名字

std::cout - ::,作用域运算符

## 从流读取数据

 std::cin>>v1>>v2;

```c++
// 读取操作符
>> 
```



# 1.3 注释简介

# 1.4 控制流

## 1.4.1 while 语句

```c++
// vim while.cpp
#include <iostream>
int main() {
    int sum=0,val=1;
    while(val <= 10) {
        sum += val;
        ++val;
    }
    std::cout<< "Sum of 1 to 10 inclusive is " << sum << std::endl;
    return 0;
}
```

```c++
while (condition)
  statement
```



## 1.4.2 for语句

- 条件中检测变量，循环体中递增变量

```c++
// vim for.cpp
#include <iostream>
int main() {
    int sum = 0;
    for (int val = 1; val <= 10; ++val)
        sum += val;
    std::cout << "Sum of 1 to 10 inclusive is " << sum << std::endl;
    return 0;
}
```



## 1.4.3 读取数量不定的输入数据

```c++
#include <iostream>
int main() {
    int sum=0, value=0;
    while(std::cin>>value)
        sum+=value;
    std::cout<< "Sum is: " << sum << std::endl;
    return 0;
}
```

### 从键盘输入文件结束符

> Windows: Ctrl+Z  Ender
>
> UNIX: Ctrl+D



## 1.4.4 if 语句

```c++
#include <iostream>
int main() {
    int currVal = 0, val =0;
    if(std::cin >> currVal){
        int cnt = 1;
        while(std::cin >> val) {
            if (val == currVal)
                ++cnt;
            else {
                std::cout << currVal << " occurs " << cnt << " times" << std::endl;
                currVal = val;
                cnt = 1;
            }
        }
    }
    return 0;
}
```



# 1.5 类简介

## 1.5.1 Sales_item类

### 关键概念：类定义了行为

### 读写Sales_item

```c++
#include <iostream>
#include "Sales_item.h"
int main(){
    Sales_item book;
    std::cin >> book;
    std::cout << book << std::endl;
    return 0;
}
```



### Sales_item对象的加法

```c++
#include <iostream>
#include "Sales_item.h"
int main(){
    Sales_item item1, item2;
    std::cin>>item1>>item2;
    std::cout<<item1+item2<<std::endl;
    return 0;
}
```



### 使用文件重定向

```bash
addItems < infile >outfile
```

### Sales_item.h

```c++
#ifndef SALESITEM_H
#define SALESITEM_H
#include <iostream>
#include <string>

class Sales_item
{
    public:
        Sales_item(const std::string &book):isbn(book),units_sold(0),revenue(0.0){}
        Sales_item(std::istream &is){ is >> *this;}
        friend std::istream& operator>>(std::istream &,Sales_item &);
        friend std::ostream& operator<<(std::ostream &,const Sales_item &);
    public:
        Sales_item & operator+=(const Sales_item&);
    public:
        double avg_price() const;
        bool same_isbn(const Sales_item &rhs)const
        {
            return isbn == rhs.isbn;
        }
        Sales_item():units_sold(0),revenue(0.0){}
    public:
        std::string isbn;
        unsigned units_sold;
        double revenue;
};

using std::istream;
using std::ostream;
Sales_item operator+(const Sales_item &,const Sales_item &);
inline bool operator==(const Sales_item &lhs,const Sales_item &rhs)
{
    return lhs.units_sold == rhs.units_sold && lhs.revenue == rhs.revenue && lhs.same_isbn(rhs);
}
inline bool operator!=(const Sales_item &lhs,const Sales_item &rhs)
{
    return !(lhs == rhs);
}

inline Sales_item & Sales_item::operator +=(const Sales_item &rhs)
{
    units_sold += rhs.units_sold;
    revenue += rhs.revenue;
    return *this;
}
inline Sales_item operator+(const Sales_item &lhs,const Sales_item &rhs)
{
    Sales_item ret(lhs);
    ret += rhs;
    return ret;
}
inline istream& operator>>(istream &in,Sales_item &s)
{
    double price;
    in >> s.isbn >> s.units_sold >> price;
    if(in)
        s.revenue = s.units_sold * price;
    else
        s = Sales_item();
    return in;
}
inline ostream& operator<<(ostream &out,const Sales_item &s)
{
    out << s.isbn << "\t" <<s.units_sold << "\t" << s.revenue << "\t" << s.avg_price();
    return out;
}
inline double Sales_item::avg_price() const
{
    if(units_sold)
        return revenue/units_sold;
    else
        return 0;
}
#endif
```



## 1.5.2　初识成员函数

```c++
item.isbn()
```

- 点运算符 `.`
- 调用运算符 `()`



# 1.6 书店程序

