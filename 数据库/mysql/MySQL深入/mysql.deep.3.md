<!-- 事务隔离 -->

SQL标准的事务隔离级别包括：读未提交（read uncommitted）、读提交（read committed）、可重复读（repeatable read）和串行化（serializable ）

实例代码
```
mysql> create table T(c int) engine=InnoDB;
insert into T(c) values(1);
```

![mysql3](https://raw.githubusercontent.com/FameLsy/Images/master/MySQL/mysql3.png)

在不同事务下，V1、V2、V3的返回值分别是
- 若隔离级别是“读未提交”， 则V1的值就是2。这时候事务B虽然还没有提交，但是结果已经被A看到了。因此，V2、V3也都是2。
- 若隔离级别是“读提交”，则V1是1，V2的值是2。事务B的更新在提交后才能被A看到。所以， V3的值也是2。
- 若隔离级别是“可重复读”，则V1、V2是1，V3是2。之所以V2还是1，遵循的就是这个要求：事务在执行期间看到的数据前后必须是一致的。
- 若隔离级别是“串行化”，则在事务B执行“将1改成2”的时候，会被锁住。直到事务A提交后，事务B才可以继续执行。所以从A的角度看， V1、V2值是1，V3的值是2。

在实现上，数据库里面会创建一个视图，访问的时候以视图的逻辑结果为准。
- 在“可重复读”隔离级别下，这个视图是在事务启动时创建的，整个事务存在期间都用这个视图。
- 在“读提交”隔离级别下，这个视图是在每个SQL语句开始执行的时候创建的
- 读未提交”隔离级别下直接返回记录上的最新值，没有视图概念；
- “串行化”隔离级别下直接用加锁的方式来避免并行访问

如果是Oracle数据库迁移到MySQL,因为Oracle默认隔离级别其实就是“读提交”,所以需要修改MySQL（将启动参数transaction-isolation的值设置成READ-COMMITTED）

查看当前事务隔离级别
```
show variables like 'transaction_isolation';
```

# 事务隔离的实现

在MySQL中，实际上每条记录在更新的时候都会同时记录一条回滚操作。

如图所示，以下是”可重复读“的回滚日志，假设一个值从1被按顺序改成了2、3、4，在回滚日志里面就会有类似下面的记录。
![mysql4](https://raw.githubusercontent.com/FameLsy/Images/master/MySQL/mysql4.png)

**不同时刻启动的事务会有不同的read-view**。如图中看到的，**当前值是4**，在视图A、B、C里面，这一个记录的值分别是1、2、4，同一条记录在系统中可以存在多个版本，就是数据库的**多版本并发控制（MVCC）**，对于read-view A，要得到1，就必须将当前值依次执行图中所有的回滚操作得到

当没有事务再需要用到这些回滚日志时，回滚日志会被删除。也就是说，当系统里没有比这个回滚日志更早的read-view的时候，回滚日志会被删除

尽量不要使用长事务
- 长事务意味着系统里面会存在很老的事务视图。由于这些事务随时可能访问数据库里面的任何数据，所以这个事务提交之前，数据库里面它可能用到的回滚记录都必须保留，这就会导致大量占用存储空间
- 在MySQL 5.5及以前的版本，回滚日志是跟数据字典一起放在ibdata文件里的，即使长事务最终提交，回滚段被清理，文件也不会变小

# 事务的启动方式

MySQL的事务启动方式有以下几种
1. 显式启动事务语句， begin 或 start transaction。配套的提交语句是commit，回滚语句是rollback
2. set autocommit=0，这个命令会将这个线程的自动提交关掉。当执行一个SQL语句，就会启动事务，而且并不会自动提交。这个事务持续存在直到你主动执行commit 或 rollback 语句，或者断开连接(如果是长连接，就会导致长事务！！)

所以，建议使用set autocommit=1，显示启动事务;通过以下方式提交，可以在提交之后，开启一个新的事务
```
COMMIT WORK AND CHAIN;
```

此外，可以在information_schema库的innodb_trx这个表中查询长事务
```
#查找持续时间超过60s的事务
select * from information_schema.innodb_trx where TIME_TO_SEC(timediff(now(),trx_started))>60
```


