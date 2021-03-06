---
title: 事务处理
tags: 
- MySQL
- 数据库
categories: 
- Database
---

# 什么是事务

事务：
- 指的是逻辑上一组操作，组成这个事务的各个执行单元，要么一起成功,要么一起
- InnoDB引擎支持

相关术语
- 事务（transaction）：指一组SQL语句；
- 回滚（rollback）：撤销指定SQL语句的过程
- 提交（commit）：将为存储的SQL语句结果写入数据库表
- 保留点（savepoint）：指事务处理中设置的临时占位符（placeholder），可以对它发布回滚（与回滚整个事务处理不同）


事务的特性(ACID)
1. 原子性（Atomicity）  
    原子性是指事务包含的所有操作要么全部成功，要么全部失败回滚
2. 一致性（Consistency）  
    一致性是指事务必须使数据库从一个一致性状态变换到另一个一致性状态，也就是说一个事务执行之前和执行之后都必须处于一致性状态(如转账，两个人怎么转，总金额是不会变的)
3. 隔离性（Isolation）  
    隔离性是当多个用户并发访问数据库时，比如操作同一张表时，数据库为每一个用户开启的事务，不能被其他事务的操作所干扰，多个并发事务之间要相互隔离
4. 持久性（Durability）  
    持久性是指一个事务一旦被提交了，那么对数据库中的数据的改变就是永久性的，即便是在数据库系统遇到故障的情况下也不会丢失提交事务的操作

# 事务并发问题（隔离性导致）
在事务的并发操作中可能会出现一些问题：
1. 脏读：一个事务读取到另一个事务未提交的数据。
2. 不可重复读：一个事务因读取到另一个事务已提交的数据。导致对同一条记录读取两次以上的结果不一致。(update操作，读取的内容改变)
3. 幻读：一个事务因读取到另一个事务已提交的数据。导致对同一张表读取两次以上的结果不一致。(insert、delete操作,返回的结果行数改变)


# 事务隔离级别

MySQL数据库为我们提供的四种隔离级别（由低到高）：
1. Read uncommitted (读未提交)：最低级别，任何情况都无法保证。
2. Read committed (读已提交)：可避免脏读的发生。
3. Repeatable read (可重复读)：可避免脏读、不可重复读的发生。[MySQL默认]
4. Serializable (串行化)：可避免脏读、不可重复读、幻读的发生。

>注意  
>隔离级别越高，越能保证数据的完整性和一致性，但是对并发性能的影响也越大。
>对于多数应用程序，可以优先考虑把数据库系统的隔离级别设为Read Committed。它能够避免脏读取，而且具有较好的并发性能。尽管它会导致不可重复读、幻读这些并发问题，在可能出现这类问题的个别场合，可以由应用程序采用悲观锁或乐观>锁来控制。


# 事务SQL

开始事务
```
START TRANSACTION
```

回滚事务
- 只能在一个事务处理内使用（在执行一条START TRANSACTION命令之后）
- 事务处理可以用来管理INSERT、UPDATE、DELETE
- 不能回滚CREATE和DROP操作

```
ROLLBACK
```

提交事务
- 数据库默认是自动提交的
- 但在事务处理快中，不会自动提交，需要手动COMMIT

```
COMMIT
```


关闭事务：
- 执行COMMIT和ROLLBACK后自动关闭


设置保留点
```
SAVEPOINT point
```

回滚到保留点
```
ROLLBACK TO point
```

释放保留点
- （执行完ROLLBACK或COMMIT后自动释放，或RELEASE SAVEPOINT明确释放）

更改系统默认的提交行为 
```java
//表示不自动提交。Mysql默认自动提交。
SET autocommit=0
```