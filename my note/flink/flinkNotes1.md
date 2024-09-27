# flink

- [Flink CDC Connectors](https://ververica.github.io/flink-cdc-connectors/release-2.3/content/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8B/mysql-postgres-tutorial-zh.html)

- 负责从 MySQL、PostgreSQL、Oracle、MongoDB 等数据库读取存量历史数据和增量变更数据



# 🐭 postgres,mysql,elasticsearch,kibana

```bash
vim docker-compose.yml
```

```yaml
version: '2.1'
services:
  postgres:
    image: debezium/example-postgres:1.1
    ports:
      - "5432:5432"
    environment:
      - POSTGRES_DB=postgres
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres
  mysql:
    image: debezium/example-mysql:1.1
    ports:
      - "3306:3306"
    environment:
      - MYSQL_ROOT_PASSWORD=123456
      - MYSQL_USER=mysqluser
      - MYSQL_PASSWORD=mysqlpw
      - "TZ=Asia/Shanghai"
    volumes:
    	- /Users/xianfan/flink/mysql/conf:/etc/mysql/conf.d
    	- /Users/xianfan/flink/mysql/data:/var/lib/mysql
      - /Users/xianfan/flink/mysql/logs:/var/log/mysql
   	
  elasticsearch:
    image: elastic/elasticsearch:7.6.0
    environment:
      - cluster.name=docker-cluster
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
      - discovery.type=single-node
      - bootstrap.system_call_filter=false
    ports:
      - "9200:9200"
      - "9300:9300"
    ulimits:
      memlock:
        soft: -1
        hard: -1
      nofile:
        soft: 65536
        hard: 65536
  kibana:
    image: elastic/kibana:7.6.0
    ports:
      - "5601:5601"
```

```bash
docker-compose up -d
docker-compose down
```







# 🐮 Install flink

```bash
https://archive.apache.org/dist/flink/flink-1.16.0/flink-1.16.0-bin-scala_2.12.tgz
https://repo.maven.apache.org/maven2/org/apache/flink/flink-sql-connector-elasticsearch7/1.16.0/flink-sql-connector-elasticsearch7-1.16.0.jar
https://repo1.maven.org/maven2/com/ververica/flink-sql-connector-mysql-cdc/2.3.0/flink-sql-connector-mysql-cdc-2.3.0.jar
https://repo1.maven.org/maven2/com/ververica/flink-sql-connector-postgres-cdc/2.3.0/flink-sql-connector-postgres-cdc-2.3.0.jar
```

```bash
aria2c -x 10 -s 10 -i uris
tar -xvf flink-1.16.0-bin-scala_2.12.tgz
mv *.jar flink-1.16.0/lib
```



# 🐯 populate data

## 1⃣️mysql

```bash
docker-compose exec mysql mysql -uroot -p123456
```

```sql
-- 创建数据库和表 products，orders，并插入数据
-- MySQL
CREATE DATABASE mydb;
USE mydb;
CREATE TABLE products (
  id INTEGER NOT NULL AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  description VARCHAR(512)
);
ALTER TABLE products AUTO_INCREMENT = 101;

INSERT INTO products
VALUES (default,"scooter","Small 2-wheel scooter"),
       (default,"car battery","12V car battery"),
       (default,"12-pack drill bits","12-pack of drill bits with sizes ranging from #40 to #3"),
       (default,"hammer","12oz carpenter's hammer"),
       (default,"hammer","14oz carpenter's hammer"),
       (default,"hammer","16oz carpenter's hammer"),
       (default,"rocks","box of assorted rocks"),
       (default,"jacket","water resistent black wind breaker"),
       (default,"spare tire","24 inch spare tire");

CREATE TABLE orders (
  order_id INTEGER NOT NULL AUTO_INCREMENT PRIMARY KEY,
  order_date DATETIME NOT NULL,
  customer_name VARCHAR(255) NOT NULL,
  price DECIMAL(10, 5) NOT NULL,
  product_id INTEGER NOT NULL,
  order_status BOOLEAN NOT NULL -- Whether order has been placed
) AUTO_INCREMENT = 10001;

INSERT INTO orders
VALUES (default, '2020-07-30 10:08:22', 'Jark', 50.50, 102, false),
       (default, '2020-07-30 10:11:09', 'Sally', 15.00, 105, false),
       (default, '2020-07-30 12:00:30', 'Edward', 25.25, 106, false);
```

## 2⃣️Postgres

```bash
docker-compose exec postgres psql -h localhost -U postgres
```

```sql
-- 创建表 shipments，并插入数据
-- PG
CREATE TABLE shipments (
  shipment_id SERIAL NOT NULL PRIMARY KEY,
  order_id SERIAL NOT NULL,
  origin VARCHAR(255) NOT NULL,
  destination VARCHAR(255) NOT NULL,
  is_arrived BOOLEAN NOT NULL
);
ALTER SEQUENCE public.shipments_shipment_id_seq RESTART WITH 1001;
ALTER TABLE public.shipments REPLICA IDENTITY FULL;
INSERT INTO shipments
VALUES (default,10001,'Beijing','Shanghai',false),
       (default,10002,'Hangzhou','Shanghai',false),
       (default,10003,'Shanghai','Hangzhou',false);
```





# 🐰启动 Flink 集群和 Flink SQL CLI

```bash
cd flink-1.16.0
./bin/start-cluster.sh
./bin/sql-client.sh
./bin/stop-cluster.sh
```

## 1⃣️开启 checkpoint，每隔3秒做一次 checkpoint

```sql
SET execution.checkpointing.interval = 3s;
```

## 2⃣️对于数据库中的表 products, orders, shipments， 使用 Flink SQL CLI 创建对应的表，用于同步这些底层数据库表的数据

- [connector-options](https://ververica.github.io/flink-cdc-connectors/master/content/connectors/mysql-cdc.html#connector-options)
  - 'server-time-zone'='UTC'
  - 'server-time-zone'='Asia/Shanghai'  -- 默认值

```sql
#  Specify the right server-time-zone for mysql-cdc
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
  
CREATE TABLE orders (
   order_id INT,
   order_date TIMESTAMP(0),
   customer_name STRING,
   price DECIMAL(10, 5),
   product_id INT,
   order_status BOOLEAN,
   PRIMARY KEY (order_id) NOT ENFORCED
 ) WITH (
   'connector' = 'mysql-cdc',
   'hostname' = 'localhost',
   'port' = '3306',
   'username' = 'root',
   'password' = '123456',
   'database-name' = 'mydb',
   'table-name' = 'orders'
 );
 
CREATE TABLE shipments (
   shipment_id INT,
   order_id INT,
   origin STRING,
   destination STRING,
   is_arrived BOOLEAN,
   PRIMARY KEY (shipment_id) NOT ENFORCED
 ) WITH (
   'connector' = 'postgres-cdc',
   'hostname' = 'localhost',
   'port' = '5432',
   'username' = 'postgres',
   'password' = 'postgres',
   'database-name' = 'postgres',
   'schema-name' = 'public',
   'table-name' = 'shipments'
 );
```

## 3⃣️最后，创建 enriched_orders 表， 用来将关联后的订单数据写入 Elasticsearch 中

```sql
CREATE TABLE enriched_orders (
   order_id INT,
   order_date TIMESTAMP(0),
   customer_name STRING,
   price DECIMAL(10, 5),
   product_id INT,
   order_status BOOLEAN,
   product_name STRING,
   product_description STRING,
   shipment_id INT,
   origin STRING,
   destination STRING,
   is_arrived BOOLEAN,
   PRIMARY KEY (order_id) NOT ENFORCED
 ) WITH (
     'connector' = 'elasticsearch-7',
     'hosts' = 'http://localhost:9200',
     'index' = 'enriched_orders'
 );
```





# 🐲关联订单数据并且将其写入 Elasticsearch 中

```sql
-- 使用 Flink SQL 将订单表 order 与 商品表 products，物流信息表 shipments 关联，并将关联后的订单信息写入 Elasticsearch 中
-- Flink SQL
INSERT INTO enriched_orders
 SELECT o.*, p.name, p.description, s.shipment_id, s.origin, s.destination, s.is_arrived
 FROM orders AS o
 LEFT JOIN products AS p ON o.product_id = p.id
 LEFT JOIN shipments AS s ON o.order_id = s.order_id;
```



# 🐍 查看信息

## 1⃣️[flink-cluster(8081)](http://localhost:8081)

## 2⃣️ [kibana(5601)]( http://localhost:5601/app/kibana#/management/kibana/index_pattern )

- `enriched_orders`  - create index pattern



# 🐎 容器端口

| 容器          | 端口                | remark       |            |
| ------------- | ------------------- | ------------ | ---------- |
| postgres      | 5432                | 40592ce68c29 |            |
| mysql         | 3306/tcp, 33060/tcp | aab044568906 |            |
| kibana        | 5601                | b48b65b9624b | 172.18.0.4 |
| elasticsearch | 9200                | 4f04dfd85064 | 172.18.0.5 |

```bash
curl http://elasticsearch:9200
curl http://127.0.0.1:9200
```



# ⚠️问题

## 1. mysql 设置连接参数，和flik cdc要一致

- 时区
- 编码

## 2. elasticsearch 9200 连不上

- 没搞清楚为什么，什么也没做有时候又连上了

  ```bash
  curl http://elasticsearch:9200
  ```

- 解决：

 ```bash
  # https://stackoverflow.com/questions/50314586/elasticsearch-fails-to-start-config-seccomp-and-config-seccomp-filter-are-neede
  bootstrap.system_call_filter: false
 ```

## 3. 重启elasticsearch后，flink-cluster总是需要重启才能工作？

- 不知道为什么？
- 输入里面的表也要重建，为什么？
- flink-cluster还会莫名其妙的就没有办法拉取数据了。。



## 4. No Elasticsearch indices match your pattern.

- [elasticsearch-image](https://github.com/kzwang/elasticsearch-image)



## 5. Flink 是如何将增量数据拉取过去的，怎么配置



# ref

- [flink cdc笔记（一）：flink cdc简介](https://blog.csdn.net/YiRan_Zhao/article/details/126946412)
- [Flink CDC Connectors](https://ververica.github.io/flink-cdc-connectors/release-2.3/content/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8B/mysql-postgres-tutorial-zh.html)
- [elasticsearch-image](https://github.com/kzwang/elasticsearch-image)
- [ ] [使用 Flink CDC 实现 MySQL 数据实时入 Apache Doris](https://developer.aliyun.com/article/920350#slide-1)
- [ ] 





# 🗺️todo

- [ ]  保持容器状态，挂载数据到外部

  - [ ] mysql
  - [ ] postgres
  - [ ] flink， flink为什么每次启动集群都被重置状态？

- [ ] mysql CDC 导入到 doris

- [ ] sqlServer CDC 导入 doris

- [ ] mysql是怎么配置的？ 

  ```bash
  image: debezium/example-mysql:1.1
  # 上面的镜像新增了什么？ 如果是一个普通的mysql镜像，需要添加什么配置？
  ```

  

- [ ] 功能复制到测试机

