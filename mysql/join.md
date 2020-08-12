# Join

# NLJ & BNJ

- 如果有两个大小不同的表做**join**，应该用小表做**驱动表**

```mysql
CREATE TABLE `t2` (
  `id` int(11) NOT NULL,
  `a` int(11) DEFAULT NULL,
  `b` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `a` (`a`)
) ENGINE=InnoDB;

drop procedure idata;
delimiter ;;
create procedure idata()
begin
  declare i int;
  set i=1;
  while(i<=1000)do
    insert into t2 values(i, i, i);
    set i=i+1;
  end while;
end;;
delimiter ;
call idata();

create table t1 like t2;
insert into t1 (select * from t2 where id<=100)
```

## Index Nested-Loop Join	NLJ

```mysql
select * from t1 straight_join t2 on (t1.a=t2.a);
```

用`straight_join`保证t1是**驱动表**，t2时**被驱动表**

这个过程如下伪代码：

```py
for row in t1:
	for a in row:
		row + t2[a]
```

所以叫`Index Nested-Loop Join`， **NLJ**

![image-20200530115758308](https://i.loli.net/2020/05/30/vrs7zpxOyoV9bEI.png)

可以看到，join过程用上了t2的a索引，这样就是



## Block Nested-Loop Join    BNL

**假设字段a上没有索引**

1. 把t1的数据读到线程内存**join_buffer**中
2. 扫描t2，把t2每行取出，跟join_buffer的数据对比，满足join条件的，作为结果集的一部分返回

![image-20200530174543324](https://i.loli.net/2020/05/30/iqkfYnHlUd74WVS.png)

> 1. 如果可以使用被驱动表的索引，join语句还是有其优势的
> 2. 不能使用被驱动表的索引，只能使用Block Nested-Loop Join算法，这样的语句就尽量不要使用
> 3. 在使用join的时候，应该让小表做驱动表。





# Join 优化:  MRR & BKA

```mysql
create table t1(id int primary key, a int, b int, index(a));
create table t2 like t1;
drop procedure idata;
delimiter ;;
create procedure idata()
begin
  declare i int;
  set i=1;
  while(i<=1000)do
    insert into t1 values(i, 1001-i, i);
    set i=i+1;
  end while;
  
  set i=1;
  while(i<=1000000)do
    insert into t2 values(i, i, i);
    set i=i+1;
  end while;

end;;
delimiter ;
call idata();
```

- a索引字段，倒序

### Multi-Range Read	MRR

- 目的: 尽量使用顺序读盘
- `set optimizer_switch="mrr_cost_based=off"`  固定使用MRR

```mysql
select * from t1 where a>=1 and a<=100;
```

- 默认`mrr_cost_based=on`

![image-20200530175919113](https://i.loli.net/2020/05/30/139Irf2lROTD85d.png)

![image-20200530180441306](https://i.loli.net/2020/05/30/PFTjD6BQ9H1m8pR.png)

注意到，id是倒序的。根据索引a，找到符合的记录，然后**回表**，所以是**按a的顺序**给的结果。

- `set optimizer_switch="mrr_cost_based=off"` 使用**MRR**

![image-20200530180555203](https://i.loli.net/2020/05/30/hMUOGXoFpJ3Vjx4.png)

![image-20200530180630135](https://i.loli.net/2020/05/30/R6xTdpJo2gya7I5.png)

注意到，MRR是按照id排序的。 过程：

> 1. 根据索引a，定位到满足条件的记录，将**id值**放入read_rnd_buffer中;
> 2. 将**read_rnd_buffer**中的id进行递增**排序**；
> 3. 排序后的id数组，依次到主键id索引中查记录，并作为结果返回。

> **MRR能够提升性能的核心**在于，这条查询语句在索引a上做的是一个范围查询（也就是说，这是一个多值查询），可以得到足够多的主键id。这样通过排序以后，再去主键索引查数据，才能体现出**“顺序性”的优势**



### Batched Key Access    BKA

- **BKA**算法，其实就是对**NLJ**算法的优化

`set optimizer_switch='mrr=on,mrr_cost_based=off,batched_key_access=on';`

复用`join_buffer`, 从表t1里一次性地多拿些 ***行*** 出来，放到**join_buffer**一起传给表t2。 

# BKA依赖于MRR



### BNL算法的性能问题

> [第33篇文章](https://time.geekbang.org/column/article/79407)



### 扩展-hash join

