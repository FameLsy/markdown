---
title: Spring-AOP-简介
tgas: 
- Spring-AOP-简介
categories: 
- Spring 
---


# 什么是AOP?  
面向侧面的程序设计（aspect-oriented programming，AOP，又译作面向方面的程序设计、观点导向编程、剖面导向程序设计）是计算机科学中的一个术语，指一种程序设计范型。该范型以一种称为侧面（aspect，又译作方面）的语言构造为基础，侧面是一种新的模块化机制，用来描述分散在对象、类或函数中的横切关注点 -- wiki

作用
1. AOP采取横向抽取机制，补充了传统纵向继承体系（OOP）无法解决的重复性代码优化（性能监视、事务管理、安全检查、缓存）
2. 将业务逻辑和系统处理的代码（关闭连接、事务管理、操作日志记录）解耦。

横向的抽取机制

![横向](https://raw.githubusercontent.com/FameLsy/Images/master/spring/横向.png)

相对的，我们平常使用的是纵向抽取机制

![纵向](https://raw.githubusercontent.com/FameLsy/Images/master/spring/纵向.png)

# 依赖

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aspects</artifactId>
    <version>5.0.7.RELEASE</version>
</dependency>
<dependency>
    <groupId>aopalliance</groupId>
    <artifactId>aopalliance</artifactId>
    <version>1.0</version>
</dependency>
```
