# ref

- [doris Quick start](https://doris.apache.org/zh-CN/docs/dev/get-starting/) 
- [Flink Doris Connector](https://doris.apache.org/zh-CN/docs/dev/ecosystem/flink-doris-connector)
- [Doris 启动失败问题解决](https://blog.csdn.net/qq_45083975/article/details/122466740)
- [flink-doris-connector 下载](https://repo.maven.apache.org/maven2/org/apache/doris/flink-doris-connector-1.16/1.3.0/)
- [fe config](https://doris.apache.org/zh-CN/docs/admin-manual/config/fe-config)
- 



# 🐶Config

## [fe config](https://doris.apache.org/zh-CN/docs/admin-manual/config/fe-config)

## [be config](https://doris.apache.org/zh-CN/docs/admin-manual/config/be-config)

## [user-property](https://doris.apache.org/zh-CN/docs/admin-manual/config/user-property)





# Doris

# 🐭[Quick start](https://doris.apache.org/zh-CN/docs/dev/get-starting/) ✅

- [**快速下载**](https://doris.apache.org/zh-CN/download/)

```bash
# https://doris.apache.org/zh-CN/download/
# check and selct version to download
cat /proc/cpuinfo | grep avx2

# download
aria2c -x 16 -s 16 -i uris
https://mirrors.tuna.tsinghua.edu.cn/apache/doris/1.2/1.2.3-rc02/apache-doris-fe-1.2.3-bin-x86_64.tar.xz https://mirrors.tuna.tsinghua.edu.cn/apache/doris/1.2/1.2.3-rc02/apache-doris-be-1.2.3-bin-x86_64.tar.xz https://mirrors.tuna.tsinghua.edu.cn/apache/doris/1.2/1.2.3-rc02/apache-doris-dependencies-1.2.3-bin-x86_64.tar.xz

# FE
# vim conf/fe.conf
priority_networks=192.168.22.175/24




./bin/start_fe.sh --daemon
./bin/stop_fe.sh
# connect
mysql -uroot -P9030 -h127.0.0.1


# 加密连接 FE?

# BE
# vim conf/be.conf
priority_networks=192.168.22.175/24
# config JAVA_HOME
# config vm.max_map_coun
sysctl -w vm.max_map_count=2000000
# start
./bin/start_be.sh --daemon
mysql -uroot -P9030 -h127.0.0.1
# link to BACKEND
ALTER SYSTEM ADD BACKEND "10.0.12.10:9050";
ALTER SYSTEM ADD BACKEND "124.222.8.90:9050";

SHOW BACKENDS\G

# 创建数据库
create database demo;

# 创建数据表
use demo;

CREATE TABLE IF NOT EXISTS demo.example_tbl
(
    `user_id` LARGEINT NOT NULL COMMENT "用户id",
    `date` DATE NOT NULL COMMENT "数据灌入日期时间",
    `city` VARCHAR(20) COMMENT "用户所在城市",
    `age` SMALLINT COMMENT "用户年龄",
    `sex` TINYINT COMMENT "用户性别",
    `last_visit_date` DATETIME REPLACE DEFAULT "1970-01-01 00:00:00" COMMENT "用户最后一次访问时间",
    `cost` BIGINT SUM DEFAULT "0" COMMENT "用户总消费",
    `max_dwell_time` INT MAX DEFAULT "0" COMMENT "用户最大停留时间",
    `min_dwell_time` INT MIN DEFAULT "99999" COMMENT "用户最小停留时间"
)
AGGREGATE KEY(`user_id`, `date`, `city`, `age`, `sex`)
DISTRIBUTED BY HASH(`user_id`) BUCKETS 1
PROPERTIES (
    "replication_allocation" = "tag.location.default: 1"
);
# ERROR 1064 (HY000): errCode = 2, detailMessage = Cluster default_cluster has no available capacity
## alter system add backend "IP地址或者hostname:9050";


# 示例数据
10000,2017-10-01,北京,20,0,2017-10-01 06:00:00,20,10,10
10000,2017-10-01,北京,20,0,2017-10-01 07:00:00,15,2,2
10001,2017-10-01,北京,30,1,2017-10-01 17:05:45,2,22,22
10002,2017-10-02,上海,20,1,2017-10-02 12:59:12,200,5,5
10003,2017-10-02,广州,32,0,2017-10-02 11:20:00,30,11,11
10004,2017-10-01,深圳,35,0,2017-10-01 10:00:15,100,3,3
10004,2017-10-03,深圳,35,0,2017-10-03 10:20:22,11,6,6

# 导入数据
curl  --location-trusted -u root: -T test.csv -H "column_separator:," http://127.0.0.1:8030/api/demo/example_tbl/_stream_load

curl  --location-trusted -u root: -T test.csv -H "column_separator:," http://w.go:8030/api/demo/example_tbl/_stream_load
```









# 🐮[安装部署](https://doris.apache.org/zh-CN/docs/dev/install/install-deploy) ❌

## Docker file install

### Dockerfile

```bash
FROM apache/incubator-doris:build-env-latest

USER root
WORKDIR /root
RUN echo 'fanxian' | passwd root --stdin

RUN yum install -y vim net-tools man wget git mysql lsof bash-completion \
        && cp /var/local/thirdparty/installed/bin/thrift /usr/bin

# 更安全的使用，创建用户而不是使用 root
RUN yum install -y sudo \
        && useradd -ms /bin/bash fanxian && echo fanxian | passwd fanxian --stdin \
        && usermod -a -G wheel fanxian

USER fanxian
WORKDIR /home/fanxian
RUN git config --global color.ui true \
        && git config --global user.email "549652152@qq.com" \
        && git config --global user.name "fanxian"

# 按需安装 zsh and oh my zsh, 更易于使用，不需要的移除
USER root
RUN yum install -y zsh \
        && chsh -s /bin/zsh fanxian
USER fanxian
RUN wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | zsh \
        && git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions \
        && git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

### bash

```bash
docker build -t doris .
docker run -it doris:latest /bin/bash
# mount outside
docker run -it -v /Users/xianfan/mavenRepo/:/home/fanxian/.m2/  -v /Users/xianfan/coding/doris/doris/:/home/fanxian/code/doris/ doris:latest /bin/bash


su fanxian
mkdir code && cd code
git clone https://github.com/apache/doris.git

# 1st compile
sh build.sh --clean --be --fe --ui

# run
mkdir meta_dir

# luanch FE
cd output/fe
sh bin/start_fe.sh --daemon

# luanch BE
cd output/be
sh bin/start_be.sh --daemon

# mysql-client connect
mysql -h 127.0.0.1 -P 9030 -u root
```







## docker install

```bash
# doris source code 
git clone https://github.com/apache/doris.git


# pull doris-installer image
docker pull apache/doris:build-env-ldb-toolchain-latest

# run image, mount src doris 
# on mine
docker run -it -v /root/.m2:/root/.m2  -v /root/doris/:/root/doris/ apache/doris:build-env-ldb-toolchain-latest
# on wjs
docker run -it -v /home/maven_repo:/root/.m2  apache/doris:build-env-ldb-toolchain-latest
# on mac
docker run -it -v /Users/xianfan/doris/source:/root/doris -v /Users/xianfan/doris/.m2:/root/.m2 apache/doris:build-env-ldb-toolchain-latest

# compile doris
cat /proc/cpuinfo | grep avx2
## support
sh build.sh --clean --be --fe
## no support
USE_AVX2=0  sh build.sh --clean --be --fe
```









# 🐯Doris介绍 ✅

- 基于 MPP 架构的高性能、实时的分析型数据库

## 使用场景

- 实时数仓 Doris
- 离线湖仓（Hive, Iceberg, Hudi 中）



### 1.报表分析

### 2.即席查询（Ad-hoc Query）

### 3.统一数仓构建

### 4.数据湖联邦查询



## 技术概述

两类进程：

- **Frontend（FE）**，主要负责 
  - 用户请求的接入
  - 查询解析规划
  - 元数据的管理
  - 节点管理
- **Backend（BE）**，主要负责
  - 数据存储
  - 查询计划的执行



## ⭕️丰富的索引结构

- Sorted Compound Key Index，可以最多指定三个列组成复合排序键，通过该索引，能够有效进行数据裁剪，从而能够更好支持高并发的报表场景
- Z-order Index ：使用 Z-order 索引，可以高效对数据模型中的任意字段组合进行范围查询
- Min/Max ：有效过滤数值类型的等值和范围查询
- Bloom Filter ：对高基数列的等值过滤裁剪非常有效
- Invert Index ：能够对任意字段实现快速检索

在存储模型方面，Doris 支持多种存储模型，针对不同的场景做了针对性的优化：

- Aggregate Key 模型：相同 Key 的 Value 列合并，通过提前聚合大幅提升性能
- Unique Key 模型：Key 唯一，相同 Key 的数据覆盖，实现行级别数据更新
- Duplicate Key 模型：明细数据模型，满足事实表的明细存储



# 🐰数据表设计

## 数据模型

### Aggregate

#### 1 导入数据聚合

#### 2 保留明细数据

```bash
# 即增加了一列 timestamp，记录精确到秒的数据灌入时间。 
# 同时，将AGGREGATE KEY设置为AGGREGATE KEY(user_id, date, timestamp, city, age, sex)
# 只要保证导入的数据中，每一行的 Key 都不完全相同，那么即使在聚合模型下，Doris 也可以保存完整的明细数据
```

#### 3 导入数据与已有数据聚合

```bash
# 数据的聚合，在 Doris 中有如下三个阶段发生
1.每一批次数据导入的 ETL 阶段。该阶段会在每一批次导入的数据内部进行聚合。
2.底层 BE 进行数据 Compaction 的阶段。该阶段，BE 会对已导入的不同批次的数据进行进一步的聚合。
3.数据查询阶段。在数据查询时，对于查询涉及到的数据，会进行对应的聚合。
```



### Unique

### Duplicate

```bash
# ”Sorted Column”
```





## 数据划分

### row &column

- column
  - Key   维度列
  - Value  指标列



### Tablet & Partition

- Table

  - 若干个Partition组成

- Partition 分区 

  - **逻辑管理** 最小单元
  - 多个Tablet在组成
  - 物理独立

- Tablet🪣   捆绑若干数据行

  - **数据移动、复制** 最小单元
  - 物理独立

  

### Sql for create

```sql
-- Range Partition

CREATE TABLE IF NOT EXISTS example_db.example_range_tbl
(
    `user_id` LARGEINT NOT NULL COMMENT "用户id",
    `date` DATE NOT NULL COMMENT "数据灌入日期时间",
    `timestamp` DATETIME NOT NULL COMMENT "数据灌入的时间戳",
    `city` VARCHAR(20) COMMENT "用户所在城市",
    `age` SMALLINT COMMENT "用户年龄",
    `sex` TINYINT COMMENT "用户性别",
    `last_visit_date` DATETIME REPLACE DEFAULT "1970-01-01 00:00:00" COMMENT "用户最后一次访问时间",
    `cost` BIGINT SUM DEFAULT "0" COMMENT "用户总消费",
    `max_dwell_time` INT MAX DEFAULT "0" COMMENT "用户最大停留时间",
    `min_dwell_time` INT MIN DEFAULT "99999" COMMENT "用户最小停留时间"
)
ENGINE=OLAP
AGGREGATE KEY(`user_id`, `date`, `timestamp`, `city`, `age`, `sex`)
PARTITION BY RANGE(`date`)
(
    PARTITION `p201701` VALUES LESS THAN ("2017-02-01"),
    PARTITION `p201702` VALUES LESS THAN ("2017-03-01"),
    PARTITION `p201703` VALUES LESS THAN ("2017-04-01")
)
DISTRIBUTED BY HASH(`user_id`) BUCKETS 16
PROPERTIES
(
    "replication_num" = "3",
    "storage_medium" = "SSD",
    "storage_cooldown_time" = "2018-01-01 12:00:00"
);


-- List Partition

CREATE TABLE IF NOT EXISTS example_db.example_list_tbl
(
    `user_id` LARGEINT NOT NULL COMMENT "用户id",
    `date` DATE NOT NULL COMMENT "数据灌入日期时间",
    `timestamp` DATETIME NOT NULL COMMENT "数据灌入的时间戳",
    `city` VARCHAR(20) NOT NULL COMMENT "用户所在城市",
    `age` SMALLINT COMMENT "用户年龄",
    `sex` TINYINT COMMENT "用户性别",
    `last_visit_date` DATETIME REPLACE DEFAULT "1970-01-01 00:00:00" COMMENT "用户最后一次访问时间",
    `cost` BIGINT SUM DEFAULT "0" COMMENT "用户总消费",
    `max_dwell_time` INT MAX DEFAULT "0" COMMENT "用户最大停留时间",
    `min_dwell_time` INT MIN DEFAULT "99999" COMMENT "用户最小停留时间"
)
ENGINE=olap
AGGREGATE KEY(`user_id`, `date`, `timestamp`, `city`, `age`, `sex`)
PARTITION BY LIST(`city`)
(
    PARTITION `p_cn` VALUES IN ("Beijing", "Shanghai", "Hong Kong"),
    PARTITION `p_usa` VALUES IN ("New York", "San Francisco"),
    PARTITION `p_jp` VALUES IN ("Tokyo")
)
DISTRIBUTED BY HASH(`user_id`) BUCKETS 16
PROPERTIES
(
    "replication_num" = "3",
    "storage_medium" = "SSD",
    "storage_cooldown_time" = "2018-01-01 12:00:00"
);

```

### 列定义

尽量选择**整型**类型。因为整型类型的计算和查找效率远高于字符串



### ⭕️[分区和分桶](https://doris.apache.org/zh-CN/docs/dev/data-table/data-partition#分区和分桶)

- Partition， 支持**Range**和**List**的划分方式
  - 分区列必须为 KEY 列
  - 在写分区值时，都需要加双引号
  - 分区数量理论上没有上限
  - 创建分区时**不可添加范围重叠**的分区

#### **Range**

- 如果导入的数据范围在这个空洞范围内，是无法导入的

- **多列分区**

  ```sql
  PARTITION BY RANGE(`date`, `id`)
  (
      PARTITION `p201701_1000` VALUES LESS THAN ("2017-02-01", "1000"),
      PARTITION `p201702_2000` VALUES LESS THAN ("2017-03-01", "2000"),
      PARTITION `p201703_all`  VALUES LESS THAN ("2017-04-01")
  )
  ```

- 对于缺省值，默认填充 `MIN_VALUE`

- 批量分区

  ```sql
  -- 批量创建按天划分的分区
  FROM ("2022-01-03") TO ("2022-01-06") INTERVAL 1 DAY
  ```

  

#### **List 分区**(包含)

```sql
# Partition 支持通过 VALUES IN (...) 来指定每个分区包含的枚举值。
VALUES IN (...)
```





- Bucket（Tablet），仅支持**Hash**划分







## ⭕️使用指南⏸️

```sql
#########################
# create user
#########################

# root login & change pw
$ mysql -uroot -P9030 -h127.0.0.1
SET PASSWORD FOR 'root' = PASSWORD('fx');

# create new user
CREATE USER 'test' IDENTIFIED BY 'test';


##################################################
# create table & import data
##################################################

# create database
CREATE DATABASE example_db;

# account grant
GRANT ALL ON example_db TO test;

# create table
# 单分区 建表方式
CREATE TABLE table1
(
    siteid INT DEFAULT '10',
    citycode SMALLINT,
    username VARCHAR(32) DEFAULT '',
    pv BIGINT SUM DEFAULT '0'
)
AGGREGATE KEY(siteid, citycode, username)
DISTRIBUTED BY HASH(siteid) BUCKETS 10
PROPERTIES("replication_num" = "1");
# 多分区 建表方式
CREATE TABLE table2
(
    event_day DATE,
    siteid INT DEFAULT '10',
    citycode SMALLINT,
    username VARCHAR(32) DEFAULT '',
    pv BIGINT SUM DEFAULT '0'
)
AGGREGATE KEY(event_day, siteid, citycode, username)
PARTITION BY RANGE(event_day)
(
    PARTITION p201706 VALUES LESS THAN ('2017-07-01'),
    PARTITION p201707 VALUES LESS THAN ('2017-08-01'),
    PARTITION p201708 VALUES LESS THAN ('2017-09-01')
)
DISTRIBUTED BY HASH(siteid) BUCKETS 10
PROPERTIES("replication_num" = "1");
# 注意事项：
1.上述表通过设置 replication_num 建的都是单副本的表，Doris建议用户采用默认的 3 副本设置，以保证高可用。
2.可以对多分区表动态的增删分区，详见 HELP ALTER TABLE; 中 Partition 相关部分。
3.数据导入可以导入指定的 Partition，详见 HELP LOAD;。
4.可以动态修改表的 Schema，详见HELP ALTER TABLE;。
5.可以对 Table 增加上卷表（Rollup）以提高查询性能，这部分可以参见高级使用指南关于 Rollup 的描述。
6.表的列的Null属性默认为true，会对查询性能有一定的影响。

# import data
## 流式导入 _stream_load
## 示例1：以 "table1_20170707" 为 Label，使用本地文件 table1_data 导入 table1 表。
curl --location-trusted -u test:test -H "label:table1_20170707" -H "column_separator:," -T table1_data http://127.0.0.1:8030/api/example_db/table1/_stream_load
# 注意事项：
1.FE_HOST 是任一 FE 所在节点 IP，8030 为 fe.conf 中的 http_port。
2.可以使用任一 BE 的 IP，以及 be.conf 中的 webserver_port 进行导入。如：BE_HOST:8040

## 示例2: 以 "table2_20170707" 为 Label，使用本地文件 table2_data 导入 table2 表。
curl --location-trusted -u root:fx -H "label:table2_20170707" -H "column_separator:|" -T table2_data http://127.0.0.1:8030/api/example_db/table2/_stream_load
# 注意事项：
1.采用流式导入建议文件大小限制在 10GB 以内，过大的文件会导致失败重试代价变大。
2.label：Label 的主要作用是唯一标识一个导入任务，并且能够保证相同的 Label 仅会被成功导入一次，具体可以查看 数据导入事务及原子性 。
3.流式导入是同步命令。命令返回成功则表示数据已经导入，返回失败表示这批数据没有导入。


# Broker 导入


##################################################
# 数据的查询
##################################################
# 简单查询
# SELECT * EXCEPT (排除列)
SELECT * except (username, citycode) FROM table1;
# join查询
# 子查询


##################################################
# 表结构变更
##################################################
SHOW ALTER TABLE COLUMN;
DESC table1;
CANCEL ALTER TABLE COLUMN FROM table1;

##################################################
# Rollup
##################################################
## Rollup 可以理解为 Table 的一个物化索引结构。物化 是因为其数据在物理上独立存储，而 索引 的意思是，Rollup可以调整列顺序以增加前缀索引的命中率，也可以减少key列以增加数据的聚合度
ALTER TABLE table1 ADD ROLLUP rollup_city(citycode, pv);
SHOW ALTER TABLE ROLLUP;
DESC table1 ALL
CANCEL ALTER TABLE ROLLUP FROM table1;


```





## Rollup与查询

## 建表实践

## 索引



# 🐲 数据导入

## 导入总览

## 导入本地数据

## 导入外部存储数据

## 订阅Kafka

## 通过外部表同步数据



# 🐍数据导出

## 导出数据

## 导出查询结果集

## 导出表结构或数据

## 数据备份



# 🐎数据更新及删除

## 数据更新

## 删除操作

## 批量删除

## Sequence列



























