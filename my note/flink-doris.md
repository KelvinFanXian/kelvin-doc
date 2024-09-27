# ref

- [doris-flink-connector](https://doris.apache.org/docs/ecosystem/flink-doris-connector/)
- [doris-type](https://doris.apache.org/docs/dev/lakehouse/multi-catalog/jdbc#column-type-mapping)
- [flink-type](https://ververica.github.io/flink-cdc-connectors/release-2.3/content/connectors/mysql-cdc.html#data-type-mapping)



# flink: dev-mysql-->m-doris

## source

```sql
-- Flink SQL
CREATE TABLE cap_payment_bill (
    id_ bigint,
		code_ varchar,
		combined_pay_id_ bigint,
		total_amount_ int,
		total_charges_ int,
		transaction_type_ varchar,
		transaction_mode_ int,
		transaction_channel_ int,
		pay_scene int,
		transactionl_status_ int,
		create_time_ TIMESTAMP,
		finish_time_ TIMESTAMP,
		company_id_ bigint,
		description_ varchar,
		extend_json_ varchar,
		trader_no_ varchar,
		is_exception_ int,
		verify int,
		bank_card_id bigint,
    PRIMARY KEY (id_) NOT ENFORCED
  ) WITH (
    'connector' = 'mysql-cdc',
    'hostname' = '8.129.219.232',
    'port' = '3306',
    'username' = 'root',
    'password' = 'pisen1',
    'database-name' = 'moredb2020test',
    'table-name' = 'cap_payment_bill'
  );
```

```sql
select * from cap_payment_bill;
```





## sink

### Doris SQL

```sql
-- doris SQL
CREATE TABLE demo.cap_payment_bill (
    id_ bigint,
		code_ varchar,
		combined_pay_id_ bigint REPLACE,
		total_amount_ int REPLACE,
		total_charges_ int REPLACE,
		transaction_type_ varchar REPLACE,
		transaction_mode_ int REPLACE,
		transaction_channel_ int REPLACE,
		pay_scene int REPLACE,
		transactionl_status_ int REPLACE,
		create_time_ DATETIME REPLACE,
		finish_time_ DATETIME REPLACE,
		company_id_ bigint REPLACE,
		description_ varchar REPLACE,
		extend_json_ varchar REPLACE,
		trader_no_ varchar REPLACE,
		is_exception_ int REPLACE,
		verify STRING REPLACE,
		bank_card_id bigint REPLACE
)
AGGREGATE KEY(`id_`, `code_`)
DISTRIBUTED BY HASH(`id_`) BUCKETS 1
PROPERTIES (
    "replication_allocation" = "tag.location.default: 1"
);
```

### Flink SQL

```sql
-- Flink SQL
-- enable checkpoint
SET execution.checkpointing.interval = 3s;

-- 支持删除事件同步(sink.enable-delete='true'),需要 Doris 表开启批量删除功能
CREATE TABLE cap_payment_bill_sink (
    id_ bigint,
		code_ varchar,
		combined_pay_id_ bigint,
		total_amount_ int,
		total_charges_ int,
		transaction_type_ varchar,
		transaction_mode_ int,
		transaction_channel_ int,
		pay_scene int,
		transactionl_status_ int,
		create_time_ TIMESTAMP,
		finish_time_ TIMESTAMP,
		company_id_ bigint,
		description_ varchar,
		extend_json_ varchar,
		trader_no_ varchar,
		is_exception_ int,
		verify BINARY(1),
		bank_card_id bigint,
    PRIMARY KEY (id_) NOT ENFORCED
  ) WITH (
     'connector' = 'doris',
	   'fenodes' = '124.222.8.90:8030',
     'table.identifier' = 'demo.cap_payment_bill',
     'username' = 'root',
     'password' = 'fx',
     'sink.properties.format' = 'json',
     'sink.properties.read_json_by_line' = 'true',
     'sink.enable-delete' = 'true',
     'sink.label-prefix' = 'doris_label_dev0'
 );
 
select * from cap_payment_bill_sink;
 
-- insert
INSERT INTO cap_payment_bill_sink SELECT id_,code_,combined_pay_id_,total_amount_,total_charges_,transaction_type_,transaction_mode_,transaction_channel_,pay_scene,transactionl_status_,create_time_,finish_time_,company_id_,description_,extend_json_,trader_no_,is_exception_,verify,bank_card_id from cap_payment_bill;

select * from cap_payment_bill_sink;
```



## temp sink

```sql
-- Flink SQL
CREATE TABLE cap_payment_bill_sink (
    id_ bigint,
		code_ varchar,
    PRIMARY KEY (id_) NOT ENFORCED
  ) WITH (
     'connector' = 'doris',
	   'fenodes' = '124.222.8.90:8030',
     'table.identifier' = 'demo.cap_payment_bill',
     'username' = 'root',
     'password' = 'fx',
     'sink.properties.format' = 'json',
     'sink.properties.read_json_by_line' = 'true',
     'sink.enable-delete' = 'true',
     'sink.label-prefix' = 'doris_label_dev1'
 );
```

```sql
SET execution.checkpointing.interval = 3s;
INSERT INTO cap_payment_bill_sink SELECT id_,code_ from cap_payment_bill;
```





# issue

## 1 Cannot read the binlog filename

```bash
[ERROR] Could not execute SQL statement. Reason:
org.apache.flink.util.FlinkRuntimeException: Cannot read the binlog filename and position via 'SHOW MASTER STATUS'. Make sure your server is correctly configured
```

- solve

```sql
show variables like 'log_bin'; -- ON
SHOW VARIABLES LIKE 'binlog_format'; -- ROW
-- to Set
SET GLOBAL log_bin = ON;
-- which my.cnf
select @@basedir;

## 重启
systemctl stop mysql
systemctl start mysql

# 定位修改位置
mysql --help --verbose | grep my.cnf
cat /etc/mysql/mysql.conf.d/mysqld.cnf 
# [mysqld]
#设置日志三种格式：STATEMENT、ROW、MIXED 。
binlog_format = ROW
#设置日志路径，注意路经需要mysql用户有权限写
log-bin = /var/log/mysql/mysql-bin.log
#设置binlog清理时间
expire_logs_days = 7
#binlog每个日志文件大小
max_binlog_size = 100m
#binlog缓存大小
binlog_cache_size = 4m
#最大binlog缓存大小
max_binlog_cache_size = 512m

# 1
server-id=1

# 2
chown -R mysql:mysql   /data/mysql/logs/
chown -R mysql:adm   /data/mysql/logs/

# 3 添加apparmor可访问目录
vim /etc/apparmor.d/usr.sbin.mysqld
/data/mysql/logs/** rw
service apparmor restart
```



## 2 flink 写入时用的内网，如何配置为外网？

```sql
org.apache.doris.flink.exception.DorisRuntimeException: org.apache.http.conn.HttpHostConnectException: Connect to 10.0.12.10:8040 [/10.0.12.10] failed: Operation timed out (Connection timed out)

```



# -----

# flink: i-mysql-->w-doris

## source

```sql
-- Flink SQL
CREATE TABLE products (
    id INT,
    name STRING,
    description STRING,
    PRIMARY KEY (id) NOT ENFORCED
  ) WITH (
    'connector' = 'mysql-cdc',
    'hostname' = 'localhost',
    'port' = '3306',
    'username' = 'root',
    'password' = '123456',
    'database-name' = 'mydb',
    'table-name' = 'products'
  );
  
select * from products;
```



## sink

### Doris SQL

```sql
-- doris SQL
CREATE TABLE demo.products_sink (
  id INTEGER NOT NULL,
  name VARCHAR(255) NOT NULL,
  description VARCHAR(512) REPLACE
)
AGGREGATE KEY(`id`, `name`)
DISTRIBUTED BY HASH(`id`) BUCKETS 1
PROPERTIES (
    "replication_allocation" = "tag.location.default: 1"
);
```

### Flink SQL

```sql
-- Flink SQL
-- enable checkpoint
SET execution.checkpointing.interval = 3s;

-- 支持删除事件同步(sink.enable-delete='true'),需要 Doris 表开启批量删除功能
CREATE TABLE products_sink (
    id INT,
    name STRING,
    description STRING,
    PRIMARY KEY (id) NOT ENFORCED
  ) WITH (
     'connector' = 'doris',
	   'fenodes' = '192.168.22.175:8030',
     'table.identifier' = 'demo.products_sink',
     'username' = 'root',
     'password' = '',
     'sink.properties.format' = 'json',
     'sink.properties.read_json_by_line' = 'true',
     'sink.enable-delete' = 'true',
     'sink.label-prefix' = 'doris_label2'
 );
 
select * from products_sink;
 
-- insert
INSERT INTO products_sink select id,name,description from products;

select * from products_sink;
```





# ------

# flink: w-doris-->w-doris

## source

```sql
-- create in doris
CREATE TABLE demo.products (
  id INTEGER NOT NULL,
  name VARCHAR(255) NOT NULL,
  description VARCHAR(512) REPLACE
)
AGGREGATE KEY(`id`, `name`)
DISTRIBUTED BY HASH(`id`) BUCKETS 1
PROPERTIES (
    "replication_allocation" = "tag.location.default: 1"
);

1,scooter,Small 2-wheel scoote
2,car battery,12V car battery
3,12-pack drill bits,12-pack of drill bits with sizes ranging from #40 to #3
4,hammer,12oz carpenter's hammer
5,hammer,14oz carpenter's hammer
6,hammer,16oz carpenter's hammer
7,rocks,box of assorted rocks
8,jacket,water resistent black wind breaker

curl  --location-trusted -u root: -T products.csv -H "column_separator:," http://w.go:8030/api/demo/products/_stream_load


-- Flink SQL
CREATE TABLE products (
    id INT,
    name STRING,
    description STRING,
    PRIMARY KEY (id) NOT ENFORCED
  ) WITH (
     'connector' = 'doris',
	   'fenodes' = '192.168.22.175:8030',
     'table.identifier' = 'demo.products',
     'username' = 'root',
     'password' = ''
 );
 
select * from products;
```

## sink

```sql
-- Doris SQL
CREATE TABLE demo.products_sink (
  id INTEGER NOT NULL,
  name VARCHAR(255) NOT NULL,
  description VARCHAR(512) REPLACE
)
AGGREGATE KEY(`id`, `name`)
DISTRIBUTED BY HASH(`id`) BUCKETS 1
PROPERTIES (
    "replication_allocation" = "tag.location.default: 1"
);

-- Flink SQL
-- enable checkpoint
SET execution.checkpointing.interval = 3s;
drop table products_sink;
CREATE TABLE products_sink (
    id INT,
    name STRING,
    description STRING,
    PRIMARY KEY (id) NOT ENFORCED
  ) WITH (
     'connector' = 'doris',
	   'fenodes' = '192.168.22.175:8030',
     'table.identifier' = 'demo.products_sink',
     'username' = 'root',
     'password' = '',
     'sink.properties.format' = 'json',
     'sink.properties.read_json_by_line' = 'true',
     'sink.enable-delete' = 'true',
     'sink.label-prefix' = 'doris_label3'
 );
 
select * from products_sink;
-- insert
INSERT INTO products_sink select id,name,description from products;
select * from products_sink;
```

# 



# issues

## Label Already Exists

```sql
org.apache.doris.flink.exception.DorisRuntimeException: org.apache.doris.flink.exception.DorisException: Load status is Label Already Exists and load job finished, change you label prefix or restore from latest savepoint!
```



## NoResourceAvailableException: Could not acquire the minimum required resources.

```bash
修改了flink conf里面的flink-conf.yaml

jobmanager.memory.process.size: 2600m


# The total process memory size for the TaskManager.
#
# Note this accounts for all memory usage within the TaskManager process, including JVM metaspace and other overhead.

taskmanager.memory.process.size: 2728m

# To exclude JVM metaspace and overhead, please, use total Flink memory size instead of 'taskmanager.memory.process.size'.
# It is not recommended to set both 'taskmanager.memory.process.size' and Flink memory.
#
 taskmanager.memory.flink.size: 2280m
```





# todo

- [ ] JVM
- [ ] 网络划分与管理
- [ ] mysql binlog
- [ ] flink 