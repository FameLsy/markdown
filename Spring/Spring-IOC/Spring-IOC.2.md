---
title: Spring-IOC-工厂
tgas: 
- Spring-IOC-工厂
categories: 
- Spring 
---

在Spring中，提供了如下
1. ApplicationContext接口
2. BeanFactory接口

[这就是Spring-IOC容器的本质]

无论是BeanFactory还是ApplicationContext，其实最终的底层BeanFactory都是DefaultListableBeanFactory

# ApplicationContext接口

ApplicationContext接口
1. 实现了BeanFactory接口
2. 实现了该接口的工厂，可以获取到容器中具体的Bean对象

ApplicationContext接口常用实现类
1. ClassPathXmlApplicationContext：它是从类的根路径下加载配置文件(推荐使用这种)
2. FileSystemXmlApplicationContext：它是从磁盘路径上加载配置文件，配置文件可以在磁盘的任意位置。
3. AnnotationConfigApplicationContext:当我们使用注解配置容器对象时，需要使用此类来创建 spring 容器。它用来读取注解

# BeanFactory接口

实现BeanFactory接口的工厂也可以获取到Bean对象，是Spring早期的创建Bean对象的工厂接口。

# 对比ApplicationContext接口与BeanFactory接口

创建Bean对象的时机不同:
1. BeanFactory采取延迟加载，第一次getBean时才会初始化Bean
2. ApplicationContext是加载完applicationContext.xml时，就创建具体的Bean对象的实例(（只对BeanDefition中描述为是单例的bean，才进行饿汉式加载）)