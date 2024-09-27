# block语法结构

```objective-c
# 声明
返回值 (^变量名)(参数列表);
# 赋值
代码块变量=^[返回值类型][(参数列表)]{
  
};
```





```objective-c
// 声明一个代码块变量，变量名为：myBlock	 语法为：返回值(^变量名)(参数列表)
int (^myBlock)(int);
// 给代码块变量myBlock赋值		语法为：变量名=^(参数列表){// 代码块体}
myBlock = ^(int num) {
  return num * 10;
};
```





# 3种block

```objective-c
- (void) viewDidLoad {
  [super viewDidLoad]
  // block: keep code segments
  // hold - any where, any time
  // anonymout function --> block --> object
  void (^block)(void) = ^{
    NSLog(@"hello block");
  };
  block();
  NSLog(@"block=%@", block); // 全局 静态：__NSGlobalBlock__  0x1
  
  int a = 10;
  // block 特性： 自动捕获变量
  // 对象 -- struct
  // 增加属性 --
  // 栈 --> 操作符重写 -- copy
  // 堆
  void (^block1)(void) = ^{
    NSLog(@"hello block - %d", a);
  };
  block1();
  NSLog(@"blcok1 = %@", block1); // __NSMallocBlock__ 堆block 0x7
  
  // void (^block)(void) = 
  // 取别名 block
  NSLog(@"%@", ^{
    NSLog(@"hello block - %d", a); // __NSStackBlock__	栈block 0x6
  });
  
  // 总结 -- 内存 -- 分类
  
}
```



# In fact

```objective-c
// declare
@implementation AppVersionManager
+(void) checkVersion: (void(^)())block{
  NSString* url = [self baseUrlWith:[NSString stringWithFormat:@"/v1/accout/getAppVersionInfo?type=ios"]];
  
}

// call
[AppVersionManager checkVersion:^{_hadShowGuidePage = true;}];
```



# ref

- https://www.tutorialspoint.com/objective_c/objective_c_blocks.htm