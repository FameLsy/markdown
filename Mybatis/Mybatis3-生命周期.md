---
title: Mybatis生命周期
date: 2019-02-16 00:00:03
tags: 
- Mybatis生命周期
categories: 
- Mybaits
---
正确理解SqlSessionFactoryBuilder、SqlSessionFactory、SqlSession、和Mapper的生命周期

# SqlSessionFactoryBuilder

生命周期：
1. 其作用是构建一个或多个SqlSessionFactory
2. 只存在于方法的局部，一旦完成了构建SqlSessionFacory，它的作用就已经结束

# SqlSessionFactory

生命周期
1. 其作用是创建SqlSession
1. 因为SqlSession是一个会话，所以每次访问数据库时都需要SqlSessionFactory创建
2. 因此SqlSessionFacory存在于MyBayis的整个生命周期内



# SqlSession

生命周期：
1. 会话，相当于一个JDBC的Connection对象。
1. 因此生命周期应该在请求数据库处理事务中
2. 存活于一个应用的请求和操作

# Mapper


生命周期：
1. 其作用是发送SQL，返回我们需要的结果，或执行SQL语句修改数据库
2. 因此属于方法级别，存在于一个SqlSession事务方法之内

# 关系图

![MyBait生命周期.png](https://raw.githubusercontent.com/FameLsy/Images/master/MyBatis/MyBait生命周期.png)