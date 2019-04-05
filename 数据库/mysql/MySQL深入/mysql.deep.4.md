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