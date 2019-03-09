---
title: Spring-IOC-简介
tgas: 
- Spring-IOC-简介
categories: 
- Spring 
---

所谓的IoC容器就是指的Spring中Bean工厂里面的Map存储结构（存储了Bean的实例）

对于 Spring 框架来说，就是由 Spring 来负责控制对象的生命周期和对象间的关系

所有的类的创建、销毁都由 Spring 来控制，也就是说控制对象生存周期的不再是引用它的对象，而是 Spring 。对于某个具体的对象而言，以前是它控制其他对象，现在是所有对象都被 Spring 控制，所以这叫控制反转。

# 所需依赖

使用Spring-IOC需要的包
```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>5.0.7.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.0.7.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-beans</artifactId>
    <version>5.0.7.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-expression</artifactId>
    <version>5.0.7.RELEASE</version>
</dependency>
```
