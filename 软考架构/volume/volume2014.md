# volume2014

# 选择题

## 1～2 PV

![image-20221007005640289](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20221007005640289.png)



## 3～4 文件管理

- 磁盘索引块
- 磁盘数据块
  - 大小均为1K
- 每个文件的索引节点中有8个地址项
  - 每个地址项大小为4B

- 故每个磁盘索引块可存放1K/4=256个物理块地址



- 直接地址索引，逻辑块号0～5
- 一级间接地址索引，逻辑块号 6～（6+256-1）=261
- 二级间接地址索引，逻辑块号262～（262-1+256*256）=65797



- 该文件系统**可表示的单个文件最大长度**是 65797+1=**65798**KB



## 5 合并规则

$A\to B,A\to C,则A\to BC$



## 7～8 关系代数

- 先连接，后投影



## 10～11 嵌入式处理器

- 不具备内存管理单元（MMU）的处理器
  - **Cortex- M3**
  - 嵌入式操作系统 **uC/OS-II** 可以运行在它上面





## 12 不正确

- ~~嵌入式数据库管理系统一般不支持多纯种迸发操作~~



## 13 IntServ集成服务

- ~~突发式服务：如果有富余的带宽，网络保证满足服务质量的需求~~
- 三种质量不同的类型
  - 保证质量的服务
  - 尽力而为的服务
  - 负载受控的服务



## 14 局域网3层

- 核心层
  - 汇聚层
    - 接入层
- ~~核心层设备负责数据包过滤、策略路由等功能~~
  - 高速骨干网，尽量避免以上降低数据包转发速率的功能



## 15 水平子系统

- 连接了干线子系统和工作区子系统



## 17 TPC-C

- TPC-C
  - **在线事务处理** 的基准程序
- TPC-D
  - 决策支持 的基准程序



## 19～21 企业资源规划

- 生产计划大纲
- 主生产计划
- **能力需求计划**
  - 能够帮助企业尽早发现企业生产能力的瓶颈，为实现企业的生产任务提供能力方面的保障



## 22～23 企业信息集成

- 实现不同**数据库系统之间**的数据交换、互操作、分布数据管理和共享信息模型定义
  - **信息集成服务**

- 能够为应用提供数据交换和访问操作，使各种不同的系统能够相互协作
  - 应用集成服务



## 24 BI

- 数据仓库、联机分析和数据挖掘



## 26 联合需求计划

JRP，Joint Requirement Planing

- ~~JPR的主要目的是对需求进行分析和验证~~
  - JRP的主要意图是收集需求



## 27～28 结构化分析方法

- 表示功能模型
  - **DFD**
- 表示行为模型
  - **状态转换图**
- 以数据字典为核心
  - 数据模型 - ER
  - 功能模型 - DFD
  - 行为模型 - 状态转换图



## 30 用户界面设计

- 3条「黄金规则」
  - 让用户拥有控制权
  - 减少用户的记忆负担
  - 保持界面一致



## 35～36 UML

- （）是逻辑视图的一次执行实例，描述了并发与同步结构
  - **进程视图**
- （）是最基本的需求分析模型
  - **用例视图**

- 5个系统视图
  - 逻辑视图
  - 进程视图
  - 实现视图
  - 部署视图
  - 用例视图



## 37 静态测试

- 「数据初始化、赋值或引用过程中的异常」属于静态分析中的（）
  - **数据流分析**
- 静态测试
  - 控制流分析
  - 数据流分析
    - 指使用控制流程图检查被测程序控制结构的过程。例如，可检查被测程序是否存在没有使用的语句或子程序、是否调用并不存在的子程序，以及是否存在无法达到的语句等
  - 接口分析
  - 表达式分析



## 38 软件调试

- 软件测试的目的是找出存在的错误
  - 软件调试的目的是定位并修正错误



## 39 单元测试

- **驱动模块**用来调用被测模块，**自顶向下**的单元测试中
  - 不需要另外编写驱动模块

- 单元测试策略
  - 自顶向下的单元测试
    - 先测试上层模块，再测试下层模块。测试下层模块时由于它的上层模块己测试过，所以不必另外编写驱动模块
  - 自底向上的单元测试
    - 先测试下层模块，再测试上层模块。测试上层模块由于它的下层模块已经测试过，所以不必另外编写桩模块
  - 孤立测试
  - 综合测试



## 40 架构-需求

- ~~软件架构设计能够帮助架构师更好地捕获和细化系统需求~~
  - 软件架构设计与系统需求是直交的，两者并无必然联系



## 41 需求模型$\to$ 架构模型

- 关注
  - 如何根据需求模型构建软件架构模型
  - 如何保证模型转换的可追踪性



## 42 架构失配

- （）失配主要包括由于系统对构件基础设施、控制模型和数据模型的假设存在冲突引起的失配
  - **构件失配**
- （）失配包括由子系统对构件交互协议、构件连接时数据格式的假设存在冲突引起的失配
  - **连接子失配**



## 43～46 4+1

- 描述对象模型
  - 逻辑视图
- 表达类的内部属性和行为，以及类集合之间的交互关系
  - 类图
- 定义对象的内部行为
  - 状态图

- **4+1**

![image-20221007114240897](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20221007114240897.png)



## 47～48 DSSA

- （）的任务是控制整个领域分析过程，进行知识获取，将获取的知识组织到领域模型中
  - **领域分析者**
- （）的任务是根据领域模型和现有系统开发出DSSA，并对DSSA的准确性和一致性进行验证
  - **领域设计者**



## 49 Facade

- 服务端程序应该在包装器外观的实例上调用需要的方法，然后将请求和请求的参数发送给（）
  - 操作系统API函数

- 提高了底层代码访问的一致性，但降低了服务端程序的调用性能



## 53 架构

- 主要由（）决定架构是否满足需求、质量需求是否在设计中得到体现
  - 用户代表与领域专家



## 55-59 质量属性-架构策略

- 可用性 - 心跳
- 可修改性 --  接口-实现分离



## 60～61 风险、非风险、敏感点和权衡点

- 「改变业务数据编码方式会对系统的性能和安全性产生影响」
  - **权衡点**
- 「假设用户请求的频率为每秒1个，业务处理时间小于30毫秒，则将请求响应时间设定为1秒钟是可以接受的」
  - **非风险**

![image-20221007121122407](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20221007121122407.png)



## 62～63 ATAM 权衡

- 主要关注系统的
  - **需求说明**
- 针对性能（）安全性和可修改性，在系统开发之前进行分析、评价与折中
  - 架构描述



## 64 著作权

- **自软件开发完成之日时**



## 66 软件商标权的保护对象

- 软件注册商标



## 67 不是利用TCP/IP漏洞发起的攻击

- SQL注入攻击



## 68 应用层安全协议

- HTTPS



## *69 应用数学

|      | 甲   | 乙   | 丙   | 丁   |
| ---- | ---- | ---- | ---- | ---- |
| 👔    | 5    | 6    | 7    | 8    |
| 👖    | 6    | 7    | 8    | 9    |

![image-20221007124403872](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20221007124403872.png)



## *70 应用数学

- 决策树分析方法
  - 先画决策树，从左至右逐步画出各个决策分支，并在各分支上标出概率值，再在最右端分别标出年获利值。
  - 然后，从右至左，计算并填写各节点处的期望收益

![image-20221007124957284](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20221007124957284.png)

![image-20221007125347028](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20221007125347028.png)



## 71～75 软件架构重构

Software architecture reconstruction is an interpretive, interactive ,and iterative process including many activities. (**Information extraction**) involves analyzing a system's existing design and imiplementation artifacts to construct a model of it. The result is used in the following activities to construct a view of the system. The database construction activity converts the (**elements and relations**) contained in the view into a standard format for storage in a database. The (**view fusion**) activity involves defining and manipulating the information stored in database to reconcile, augment, and establish connections between the elements . Reconstruction consists of two primary activities: (**visualization and interaction**) and (**pattern definition and recognition**). The former provides a mechanism for the user to manipulate architectural elements, and the latter provides facilities for architecture reconstruction.



- 重构2个活动
  - 可视化和交互
  - 模式定义和识别