<!-- 索引 -->

# 索引的常见模型

三种常见索引数据结构
- 哈希表:适用于只有等值查询的场景,比如Memcached及其他一些NoSQL引擎
- 有序数组:只适用于静态存储引擎，比如要保存的是2017年某个城市的所有人口信息,这类不会再修改的数据
- 搜索树

# InnoDB 的索引模型

在InnoDB中，表都是根据主键顺序以索引的形式存放的，这种存储方式的表称为**索引组织表**。InnoDB使用了B+树索引模型，所以数据都是存储在B+树中的。每一个索引在InnoDB里面对应一棵B+树。

```
# 主键列为ID的表，表中有字段k，并且在k上有索引
mysql> create table T(
id int primary key, 
k int not null, 
name varchar(16),
index (k))engine=InnoDB;
```

表中R1~R5的(ID,k)值分别为(100,1)、(200,2)、(300,3)、(500,5)和(600,6)，两棵树的示例示意图如下

![mysql5](https://raw.githubusercontent.com/FameLsy/Images/master/MySQL/mysql5.png)

从图中可以看出，索引类型分为两类
- 主键索引:主键索引的叶子节点存的是**整行数据**。在InnoDB里，主键索引也被称为聚簇索引（clustered index）
- 非主键索引:非主键索引的叶子节点内容是**主键的值**。在InnoDB里，非主键索引也被称为二级索引（secondary index）

基于主键索引和普通索引的查询的区别
- 如果语句是select * from T where ID=500，即主键查询方式，则只需要搜索ID这棵B+树；
- 如果语句是select * from T where k=5，即普通索引查询方式，则需要先搜索k索引树，得到ID的值为500，再到ID索引树搜索一次。这个过程称为**回表**
- 非主键索引的查询需要多扫描一棵索引树，所以尽量使用组件索引

# 索引维护

B+树为了维护索引有序性，在插入新值的时候需要做必要的维护

主键长度越小，普通索引的叶子节点就越小，普通索引占用的空间也就越小，从性能和存储空间方面考量，自增主键往往是更合理的选择

# 回表

有如下初始化数据库语句：

```mysql
create table T (
ID int primary key,
k int NOT NULL DEFAULT 0, 
s varchar(16) NOT NULL DEFAULT '',
index k(k))
engine=InnoDB;

insert into T values(100,1, 'aa'),(200,2,'bb'),(300,3,'cc'),(500,5,'ee'),(600,6,'ff'),(700,7,'gg');
```

![mysql5](https://raw.githubusercontent.com/FameLsy/Images/master/MySQL/mysql5.png)

执行如下查询语句
```
select * from T where k between 3 and 5
```

它的执行流程如下
1. 在k索引树上找到k=3的记录，取得 ID = 300；
2. 再到ID索引树查到ID=300对应的R3；
3. 在k索引树取下一个值k=5，取得ID=500；
4. 再回到ID索引树查到ID=500对应的R4；
5. 在k索引树取下一个值k=6，不满足条件，循环结束。

回到主键索引树搜索的过程，称为**回表**,可以看出上述查询回表了两次(2和4)

如何通过索引优化，避免回表？
1. 覆盖索引
2. 最左前缀原则

## 覆盖索引

如果将执行语句改为
```
select ID from T where k between 3 an
```


这时只需要查ID的值，而ID的值已经在k索引树上了，因此可以直接提供查询结果，不需要回表;在这个查询里面，索引k已经“覆盖了”我们的查询需求，称为**覆盖索引**

问题：在一个市民信息表上，是否有必要将身份证号和名字建立联合索引？

```
CREATE TABLE `tuser` (
  `id` int(11) NOT NULL,
  `id_card` varchar(32) DEFAULT NULL,
  `name` varchar(32) DEFAULT NULL,
  `age` int(11) DEFAULT NULL,
  `ismale` tinyint(1) DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `id_card` (`id_card`),
  KEY `name_age` (`name`,`age`)
) ENGINE=InnoDB
```

身份证号是市民的唯一标识,根据身份证号查询市民信息的需求，只要在身份证号字段上建立索引就够了。而再建立一个（身份证号、姓名）的联合索引,有以下好处
- 如果现在有一个高频请求，要根据市民的身份证号查询他的姓名，这个联合索引就有意义了。它可以在这个高频请求上用到覆盖索引，不再需要回表查整行记录，减少语句的执行时间
- (注意：索引字段的维护总是有代价,，在建立冗余索引来支持覆盖索引时需要权衡考虑)

## 最左前缀原则

如果为每一种查询都设计一个索引，索引将会过多

继续以市民表为例，当需求变为根据身份证号查询家庭地址（业务出现概率不频繁），普通做法有
- 直接走全表扫描(没有索引，慢)
- 单独为一个不频繁的请求创建一个（身份证号，地址）的索引（有索引，但索引会越来越多）

以上两种做法都太过于浪费，但B+树这种索引结构，可以利用索引的“最左前缀”，来定位记录;最左前缀可以是联合索引的最左N个字段，也可以是字符串索引的最左M个字符

如，使用（name，age）这个联合索引,可以看到，索引项是按照索引定义里面出现的字段顺序排序的

![mysql7](https://raw.githubusercontent.com/FameLsy/Images/master/MySQL/mysql7.jpg)

那么，想要查的是所有名字第一个字是“张”的人，SQL语句的条件是"where name like ‘张%’，这样做的好处就是
- 没有为name建立索引(减少了一个索引)
- 但是通过(name, age)索引加快了查询
- 所以我们在建立联合索引的时候,如果通过调整顺序，可以少维护一个索引，那么这个顺序往往就是需要优先考虑采用的

在回到根据身份证号查询家庭地址,业务上用的多的是(身份证，姓名)这个联合索引，完全可以通过使用“最左前缀”，加快查询。

## 索引下推

以市民表的联合索引（name, age）为例。如果现在有一个需求：检索出表中“名字第一个字是张，而且年龄是10岁的所有男孩”

```
mysql> select * from tuser where name like '张%' and age=10 and ismale=1;
```

没有索引下推优化前
1. 从ID3开始一个个进行回表(InnoDB并不会去看age的值,只是按顺序把“name第一个字是’张’”的记录一条条取出来回表)
2. 到主键索引上找出数据行
3. 再对比字段值(age是否等于10，ismale)
![mysql8](https://raw.githubusercontent.com/FameLsy/Images/master/MySQL/mysql8.jpg)

在MySQL 5.6 引入的索引下推优化后
1. InnoDB在(name,age)索引内部就判断了age是否等于10,对于不等于10的记录，直接判断并跳过
2. 等于10的值再回表
3. 到主键索引上找出数据行
4. 再对比字段值(ismale)
![mysql9](https://raw.githubusercontent.com/FameLsy/Images/master/MySQL/mysql9.jpg)

# 索引选择

以市民系统为例，如果利用身份证做索引，但由于身份证号字段比较大，并不建议把身份证号当做主键，那么只能
1. 创建唯一索引
2. 创建普通索引

从性能的角度考虑，哪个更高？

![mysql5](https://raw.githubusercontent.com/FameLsy/Images/master/MySQL/mysql5.png)

## 查询过程

以上图为例，执行
```
select id from T where k=5
```

搜索步骤
- 对于普通索引来说，查找到满足条件的第一个记录(5,500)后，需要查找下一个记录，直到碰到第一个不满足k=5条件的记录。
- 对于唯一索引来说，由于索引定义了唯一性，查找到第一个满足条件的记录后，就会停止继续检索


InnoDB的数据是按数据页为单位来读写的。也就是说，当需要读一条记录的时候，并不是将这个记录本身从磁盘读出来，而是以页为单位，将其整体读入内存。在InnoDB中，每个数据页的大小默认是16KB（linux内存页大小为4k）

因为引擎是按页读写的，所以说，当找到k=5的记录的时候，它所在的数据页就都在内存里了。那么，对于普通索引来说，要多做的那一次“查找和判断下一条记录”的操作，就只需要一次指针寻找和一次计算。

如果k=5这个记录刚好是这个数据页的最后一个记录，那么要取下一个记录，必须读取下一个数据页，这个操作会稍微复杂一些。但是，对于整型字段，一个数据页可以放近千个key，因此出现这种情况的概率会很低。所以，我们计算平均性能差异时，仍可以认为这个操作成本对于现在的CPU来说可以忽略不计。

**结论：两种索引对查询过程带来的性能差距不大**

## 更新过程

先了解什么是**change buffer**
1. 当更新数据时，如果数据页在内存中就直接更新
2. 如果这个数据页还没有在内存中的话，在不影响数据一致性的前提下，InooDB会将这些**更新操作**缓存在change buffer中，不执行从磁盘中读入这个数据页。在下次查询需要访问这个数据页的时候，将数据页读入内存，执行change buffer中操作
3. 将change buffer中的操作应用到原数据页，得到最新结果的过程称为merge  
  a. 访问数据页会触发merge(即2中的方式)  
  b. 系统有后台线程会定期merge  
  c. 在数据库正常关闭（shutdown）的过程中，也会执行merge操作
4. change buffer的大小，可以通过参数innodb_change_buffer_max_size来动态设置（如50，表示change buffer的大小最多只能占用buffer pool的50%）

利用**change buffer**的好处
1. 将更新操作先记录在change buffer，减少读磁盘，语句的执行速度会得到明显的提升
2. 数据读入内存是需要占用buffer poo,这种方式还能够避免占用内存，提高内存利用率

唯一索引的更新就不能使用**change buffer**
- 所有的更新操作都要先判断这个操作是否违反唯一性约束(如要插入(4,400)这个记录，就要先判断现在表中是否已经存在k=4的记录，而这必须要将数据页读入内存才能判断)，既然读入了内存，自然不需要**change buffer**

性能对比，对于数据页已经在内存中读入的，仅仅只是多了步判断，差别不大
- 对于唯一索引来说，找到3和5之间的位置，**判断到没有冲突**, 插入这个值，语句执行结束；
- 对于普通索引来说，找到3和5之间的位置，插入这个值，语句执行结束

如果数据页没有在内存中读入
- 对于唯一索引来说，需要将数据页读入内存，判断到没有冲突，插入这个值，语句执行结束；
- 对于普通索引来说，则是将更新记录在change buffer，语句执行就结束了

很明显，普通索引利用**change buffer**减少了随机磁盘访问，对更新性能的提升明显

**结论：更新过程利用普通索引性能更好**

## 正确的选择

一般情况下，对于查询，无论时普通索引和唯一索引性能几乎无差，但对于更新操作，需要考虑如下
- 一般情况下，尽量选择普通索引
- 如果所有的更新后面，都马上伴随着对这个记录的查询,应该关闭change buffer

## change buffer 和 redo log

关于redo log和WAL，请看[SQL更新语句执行过程]()章节

WAL和change buffer提升性能的核心机制，也的确是尽量减少随机读写，概念容易混淆

执行如下语句,假设k1数据页在内存中，而K2数据页不在内存中
```
mysql> insert into t(id,k) values(id1,k1),(id2,k2);
```

从图中可以看，该语句分为四个部分:内存、redo log（ib_log_fileX）、 数据表空间（t.ibd）、系统表空间（ibdata1）

![mysql17](https://raw.githubusercontent.com/FameLsy/Images/master/MySQL/mysql17.png)

更新语句主要执行了以下操作
1. Page 1在内存中，直接更新内存；
2. Page 2没有在内存中，就在内存的change buffer区域，记录下“我要往Page 2插入一行”这个信息
3. 将上述两个动作记入redo log中（图中3和4）

该操作一共执行了两次内存写入（1、2），一次磁盘写入（3） ，且图中的两个虚线箭头，是后台操作，不影响更新的响应时间

之后，执行一次查询请求
```
select * from t where k in (k1, k2)
```

如果读语句发生在更新语句后不久，内存中的数据都还在，那么此时的这两个读操作就与系统表空间（ibdata1）和 redo log（ib_log_fileX）无关

如下图所示
1. 读Page 1的时候，直接从内存返回
2. 要读Page 2的时候，需要把Page 2从磁盘读入内存中，然后应用change buffer里面的操作日志，生成一个正确的版本并返回结果，此时这个数据页才会被读入内存

![mysql18](https://raw.githubusercontent.com/FameLsy/Images/master/MySQL/mysql18.png)

两者区别在于: 
- redo log 主要节省的是**随机写磁盘**的IO消耗（转成顺序写）
- change buffer主要节省的则是**随机读磁盘**的IO消耗

<!-- # 索引错选

在MySQL中一张表其实是可以支持多个索引的,但具体使用哪个索引，并不是使用者来指定，而是通过优化器的来选择的

## 索引错选示例

建表语句:

```
CREATE TABLE `t` (
  `id` int(11) NOT NULL,
  `a` int(11) DEFAULT NULL,
  `b` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `a` (`a`),
  KEY `b` (`b`)
) ENGINE=InnoDB；
```

往表t插入10万行记录从(1,1,1)到(100000,100000,100000)
```
delimiter ;;
create procedure idata()
begin
  declare i int;
  set i=1;
  while(i<=100000)do
    insert into t values(i, i, i);
    set i=i+1;
  end while;
end;;
delimiter ;
call idata();
```

通过以下语句查看,使用的是a索引

```
explain select * from t where a between 10000 and 20000;
```

![mq1](https://raw.githubusercontent.com/FameLsy/Images/master/MySQL/mq1.png)


接下来，如下图所示执行
1. Session A:开启事务
2. Session B:删除数据，调用存储过程重新插入10W行数据

![mq2](https://raw.githubusercontent.com/FameLsy/Images/master/MySQL/mq2.png)

```
# Time: 2019-04-08T08:02:08.692765Z
# User@Host: root[root] @ localhost []  Id:     4
# Query_time: 0.009771  Lock_time: 0.000132 Rows_sent: 10001  Rows_examined: 10001
SET timestamp=1554710528;
select * from t where a between 10000 and 20000;
# Time: 2019-04-08T08:02:14.660639Z
# User@Host: root[root] @ localhost []  Id:     4
# Query_time: 0.009404  Lock_time: 0.000126 Rows_sent: 10001  Rows_examined: 10001
SET timestamp=1554710534;
select * from t force index(a) where a between 10000 and 20000;
``` -->