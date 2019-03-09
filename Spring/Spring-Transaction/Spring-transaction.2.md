---
title: Spring框架的事务管理相关的类和API
tgas: 
-  Spring框架的事务管理相关的类和API
categories: 
- Spring 
---

# Spring框架的事务管理相关的类和API

![Spring事务管理接口](https://raw.githubusercontent.com/FameLsy/Images/master/spring/Spring%E4%BA%8B%E5%8A%A1%E7%AE%A1%E7%90%86%E6%8E%A5%E5%8F%A3.png)

介绍：
1. PlatformTransactionManager接口:平台事务管理器.(真正管理事务的类)。该接口有具体的实现类，根据不同的持久层框架，需要选择不同的实现类！
2. TransactionDefinition接口：事务定义信息（包括事务的隔离级别,传播行为,超时,只读等）
3. TransactionStatus接口：事务的状态（包括是否新事务、是否已提交、是否有保存点、是否回滚）
4. 关系：PlatformTransactionManager真正管理事务对象.根据事务定义的信息TransactionDefinition 进行事务管理，在管理事务中产生一些状态.将状态记录到TransactionStatus中

## PlatformTransactionManager接口

实现类:
1. DataSourceTransactionManager实现类:适用Spring的JDBC模板或者MyBatis（IBatis）框架
2. HibernateTransactionManager实现类:适用Hibernate的框架

常用方法:
1. void commit(TransactionStatus status) 
2. TransactionStatus getTransaction(TransactionDefinition definition) 
3. void rollback(TransactionStatus status) 

## TransactionDefinition

包含的事务隔离级别的常量：
1.  static int ISOLATION_DEFAULT（采用数据库的默认隔离级别）
2. static int ISOLATION_READ_UNCOMMITTED 
3. static int ISOLATION_READ_COMMITTED 
4. static int ISOLATION_REPEATABLE_READ 
5. static int ISOLATION_SERIALIZABLE 

事务的传播行为：解决的是业务层之间的方法调用

事务的传播行为常量（不用设置，使用默认值，以A调用B为例）：
1. PROPAGATION_REQUIRED（默认值） -- A中有事务,使用A中的事务.如果没有，B就会开启一个新的事务,将A包含进来.(保证A,B在同一个事务中)，默认值！！
2. PROPAGATION_SUPPORTS          -- A中有事务,使用A中的事务.如果A中没有事务.那么B也不使用事务.
3. PROPAGATION_MANDATORY         -- A中有事务,使用A中的事务.如果A没有事务.抛出异常.
4. PROPAGATION_REQUIRES_NEW      -- A中有事务,将A中的事务挂起.B创建一个新的事务.(保证A,B没有在一个事务中)
5. PROPAGATION_NOT_SUPPORTED     -- A中有事务,将A中的事务挂起.
6. PROPAGATION_NEVER             -- A中有事务,抛出异常.
7. PROPAGATION_NESTED            -- 嵌套事务.当A执行之后,就会在这个位置设置一个保存点.如果B没有问题.执行通过.如果B出现异常,运行客户根据需求回滚(选择回滚到保存点或者是最初始状态)

