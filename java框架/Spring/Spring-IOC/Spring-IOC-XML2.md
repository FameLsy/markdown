---
title: < bean >标签详解
tags: 
- < bean >标签详解
categories: 
- Spring 
---


bean标签详解

作用：
1. 用于配置对象让 spring 来创建的
2. 默认情况下它调用的是类中的无参构造函数。如果没有无参构造函数则不能创建成功

属性:
1. id:唯一标识
2. class:指定类的全限定类名。用于反射创建对象。默认情况下调用无参构造函数
3. scope指定对象的作用范围[1]
4. init-method：指定类中的初始化方法名称
5. destroy-method：指定类中销毁方法名称。比如DataSource的配置中一般需要指定destroy-method=“close”
5. factory-method:指定生产对象的静态方法(用于通过静态工厂实现bean的实例化)

>注[1]  
>scope的作用范围  
>singleton :默认值，单例的（在整个容器中只有一个对象）.
>prototype :多例  
>request:Web 项目中,Spring 创建一个 Bean 的对象,将对象存入到 request 域中  
>session:Web 项目中,Spring 创建一个 Bean 的对象,将对象存入到 session 域中  
>global session	:Web 项目中,应用在 Portlet 环境.如果没有 Portlet 环境那么globalSession 相当于 session.

