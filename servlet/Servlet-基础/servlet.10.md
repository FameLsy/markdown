---
title: Servlet-生命周期
tags: 
- Servlet-生命周期
categories: 
- Servlet
---

# 完整的生命周期

概述：
1. Tomcat在启动的时候，会根据loadn-on-startup的值来创建Servlet(大于1: 启动时初始化；等于0，第一次请求时初始化)
2. Servlet对象会处理所有客户的请求，且为单例形式，只有当服务器关闭时，才销毁该对象
3. 当多个客户端并发请求Servlet时，服务器会启动多个线程分别执行service()方法

相关方法
1. init (): Servlet通过该方法进行初始化
2. service：Servlet通过该方法处理客户端请求
3. destroy():通过该方法终止
4. JVM垃圾回收

init():
1. 创建和加载数据，这些数据存在于Serlvet的整个生命周期
2. 只被调用一次
3. 在Saerlvet第一次创建时调用


service():
1. 用于处理客户掉的请求，根据HTTP的请求类型，来觉得调用doGet()或doPost()
2. 无需改动，修改方法直接重载doGet()或doPost()

doGet():
1. 处理GET请求

doPost():
1. 处理POST请求
2. 一种常用做法时在doPost()里调用doGet(),达到代码复用

destroy:
1. 关闭数据库连接，停止后台线程，把Cooike列表或点击计数器写入磁盘，并执行其他类的清理
2. 调用该方法后，Servlet对象被比标记为回收，等待JVM清理

# 注解使用

在JavaEE5 规范开始后，Servlet增加了两个影响Servlet生命周期的注解
1. @PostConstrut
2. @PreDestroy

@PostConstrut:
1. 被该注解的方法在加载Servlet时运行,只调用一次
2. 方法运行于构造函数之后，init()之前

@PreDestroy:
1. 方法在服务器销毁Serlvet时运行，只运行一次
2. 运行于destroy()方法之后，Servlet被彻底销毁之前

添加注解后的生命周期:

![生命周期](https://raw.githubusercontent.com/FameLsy/Images/master/servlet/生命周期.png)

>注：  
>两个注解修饰于一个非静态的void方法  
>方法不能抛出任何异常  

注解如何被识别
1. 服务器在启动时会遍历classes目录下class文件以及WEB-INF/lib下的jar文件，以检查那些类用了注解
2. 所以这样做或多或少会影响服务器启动速度
3. 可以手动设置关闭注解扫描

关闭注解扫描  
在web.xml的< web-app >中设置< metadata-complete >属性,true为关闭注解扫描
```xml
<!-- web.xml -->
<web-app ...
         metadata-complete="true">
```

