# volume2015

# 选择题

## 1～2 PV

- Pi
- Tj
- Temp=pi
- x=Tj

![image-20221006113109864](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20221006113109864.png)

- 初始化时系统应将信号量S赋值为（）？
  - 因为公共数据单元是一个临界资源，最多允许1个终端进程使用，因此需要设置一个互斥信号量S，初值等于1
- Pi进程的工作流程如下图所求，若用P操作和V操作实现里程间的同步与互斥，则图中空a,b,c处应分别填入？
  - P(S), V(S)和V(S)
    - 进入临界区时执行P操作，退出临界区时执行V操作



## 3～4 逻辑地址 --> 物理地址

![image-20221006115845049](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20221006115845049.png)

- 逻辑地址（3）不能转换为对应的物理地址？
  - (0, 810) 和 (4, 120)
  - 段地址(x, y)，其中：x为段号，y为段内地址
    - x段号 -- 段长 
      - y段内地址 < 段长，物理地址 = 基地址 - 段内地址y
      - 否则，地址越界

- 不能转换为对应的物理地址的原因？
  - 逻辑地址到物理地址转换时地址越界



## 5 死锁



## 6 分布式数据库

- 是指局部数据模型透明，即用户或应用程序无需知道局部场地使用的是哪种**数据模型**。
  - 逻辑透明



## 7～8 关系代数

- 元组个数 - 行数

- R.A，R.D 题目有问题



## 9 BSP

- 板级支持包BSP- Board Support Package 作为对硬件的抽象，实现了
  - 硬件有关性，操作系统无关性



## *10 不是嵌入式特点

- ~~用于特定领域，不需要支持多任务~~
- 可靠性高，无需人工干预独立运行，并处理各类事件和故障
- 面向应用，可以进行裁剪和移植
- 要求编码体积小，能够在嵌入式系统的有效存储空间内运行



## 11 可移植性--硬件无关性



## 12 半双工-全双工

- 半双工总线可在两个方向上轮流传输信息
- 全双工总线可在两个方向上同时传输信息



## 13～14 RAID 5阵列

- RAID- Redundant Array of Independent Disks，独立冗余磁盘阵列
- RAID5: 两块存数据，一块存另外两块硬盘的交易校验结果
  - 坏掉一块硬盘，可以通过另外**两块**硬盘的数据算出第三块的，所以至少要3块
  - **以较小的**盘的容量为计算方式



## 15 IPv6

- IPv6的地址长度为128比特✅
- IPv6数据包的首部比IPv4复杂❌
- IPv6的地址分为单播、广播和任意播3种❌
  - 广播 --> 组播
- 每个主机拥有唯一的IPv6地址❌



## 17 评测的准确程度最低

- 合成基准程序



## 18～19 需求/供应信息流

- 供应商 -- 制造商 -- 分销商 - 零售商

- 需求信息流（需 --> 供）， 引发物流
  - 客户订单
  - **生产计划**
  - 采购合同
- 供应信息流 （供 --> 需）
  - 入库单
  - **完工报告单**
  - 库存记录
  - 可供销售量
  - 提货发运单
  - 物料



## *20 电子政务

- 主要应用模式不包括
  - ~~政府对客户~~
- 主要模式有4种：
  - 政府对政府
  - 政府对公务员
  - 政府支企业
  - 政府对公民



## 21 电子商务

- 电子商务系统中参与电子商务活动的实体包括
  - 客户、商户、银行和认证中心



## 22～24 BI

- 数据预处理 - **数据抽取、转换和装载**
- 数据分析 - **联机分析处理** 和 **数据挖掘**技术



## 25 项目范围管理

- 项目范围定义是生产项目计划的基础



## 26 配置管理，配置项的状态

- 草稿、正式发布和正在修改



## 27 不满足好的需求陈述

- ~~所有需求都就被视为同等重要~~



## 28～29 需求变更

<img src="https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20221006151624830.png" alt="image-20221006151624830" />

- 1，2，3处填写
  - 问题分析与变更描述，
  - 变更分析与成本计算
  - 变更实现

- 变更控制自动化工具应具有的**特性**
  - 记录每一个状态变更的日期及变更者



## 30 处理流程设计工具

- ~~程序流程图（PFD）用于描述系统中每个模块的输入，输出和数据加工~~
- IPO图的主体是处理过程说明，可以采用流程图、判定树/表等来进行描述✅
- 问题分析图（PAD）包含5种基本控制结构，并允许递归使用



## 31 用例

- use case用来描述系统对事件做出响应时所采取的行动。
- 包含关系
  - 从两个以上的用例中提取公共行为
- 扩展关系
  - 一个用例明显地混合了两种以上的**不同场景**，即根据情况可能发生多种分支，则可以将这个用例分为
    - 一个基本用例
    - 一或多个扩展用例
- 泛化关系
  - 当多个用例共同拥有一种类似的结构和行为的时候，可以将它们的共性抽象成为父用例，其他的用例作为泛化关系中的子用例
  - 在泛化关系中，子用例是父用例是一种特殊形式



## 32～33 Bridge pattern

- 柄体模式（Handle and Body）
- 接口模式（Interface）

![image-20221006155526884](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20221006155526884.png)

- Abstraction
  - Shape
- Implementor
  - Drawing

---

![image-20221006160029021](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20221006160029021.png)

- 优于多重继承
  - 多重继承往往违背了类的单一职责原则



## 34～35 RUP

- 迭代和增量
  - 在软件开发的早期就可以对关键的，影响大的风险进行处理



## 36 依赖倒置

- 抽象不应该依赖于细节，细节应该依赖于抽象



## 37 遗留系统 - 演化策略

![image-20221006162040908](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20221006162040908.png)

- 各象限策略
  - 一 高水平、高价值 - 改造策略
  - 二 高水平、低价值 - **集成策略**
  - 三 低水平、低价值 - 淘汰策略
  - 四 低水平、高价值 - 继承策略



## 38～39 集成测试

- 集成测试
  - 其测试的技术依据是
    - 软件概要设计文档



## 40～41 架构

- 架构反映共同的 结构和**语义特性**
- 强调对架构**设计**的重用



## 42 质量属性

- 如性能、安全性和可修改性



## 43 ADL的组成

- ADL，Architecture Description Language，架构描述语言
  - 组件
  - 组件接口
  - **连接件**
  - 架构配置



## *44～45 ABSD

ABSD，Architecture Based Software Development，基于架构的软件开发

- 商业、质量和功能需求的组合驱动软件架构设计
- 描述软件架构
  - **视角与视图**
- 描述需求
  - **用例与质量场景**



## 46 规则系统-架构风格

- 根据外部事件进行响应的场景
  - 规则系统比较适合根据外部事件，以自身状态为基础自动进行处理和动作的场景



## 47 黑板-架构风格

- 语音识别系统是一个十分典型的专家系统，其特点是
  - 求解的正确结果不止一个
  - 求解过程比较复杂
  - 需要通过专家知识和反馈逐步得到正确结果



## 48 过程控制-架构风格

- 控制系统，特点
  - 不断采集系统当前状态，与系统中的设定状态进行对比
  - 并通过将当前状态与设定状态进行对比从而进行控制



## 49 解释器-架构风格

- 用户**自行定义**游戏对象属性，行为和对象之间的交互关系



## *50 数据仓储-架构风格

- 特点
  - 以软件代码为中心进行对应的编译处理与辅助操作



## 51～52 架构设计

- 架构设计主要关注软件组件的
  - 结构
  - 属性
  - **交互作用**

- 并通过多种 **视图** 全面描述特定系统的架构



## 53～55 DSSA

- 特定问题领域为对象，形成由
  - 领域参考模型
  - 参考需求
  - **参考架构**
    - 组成的开发基础架构
- 活动
  - 领域分析 - 领域模型
  - 领域设计 - 特定领域软件架构
  - 领域实现 - 开发和组织可重用信息，并实现基础软件架构



## 56～61 质量属性-架构策略

- 可用性 - 主动冗余
- 性能 - 资源调度
- 安全性 - 追踪审计



## 62～63 架构权衡

- 场景和需求收集
- **架构视图和场景实现**
- 属性模型构造和分析
- 属性模型折中



- ATAM方法要求在系统开发之前，首先对这些质量属性进行
  - **评价**和折中



## 67～68 网络规划

- 业务的连续性得到保障
- ~~缺少网闸，不能实现财务系统与Internet的物理隔离~~



## 69 指派问题

![image-20221007001959102](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20221007001959102.png)

- 工件B应由 **丁** 加工

  ![image-20221007002708525](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20221007002708525.png)

- 对于该矩阵，并不存在全0指派。位于（1，3）、（2，1）、（3，4）、（4，2）的元素之和为1是最小的。

- 因此，分配甲、乙、丙、丁分别加工C、A、D、B能达到最少的总工时28+1=29



## 70 概率

![image-20221007003619673](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20221007003619673.png)

1，2，3，5，7





## 71～75

The objective of (**architecture design**) is to determine what parts of the application software will be assigned to what hardware. The major software components of the system being developed have to be identified and then allocated to the various hardware components on which the system will operate. All software systems can be divided into four basic functions. The first is (**data storage**) . Most infomation systems require data to be stored and retrieved, whether a small file, such as a memo producted by a word processor, or a large database, such as one that stores an orgnization's accounting records. The second function is the (**data access logic**), the processing required to access data, which often means database queries in Structrued Query Language. The third function is the (**application logic**), which is the logic documented in the DFDs, use cases, and fuctional requirements. The fourth function is the presentation logic, the display of information to the user and the acceptance of the user's commands. The three primary hardware components of a system are (**CPUs, memories and I/O devices**).

