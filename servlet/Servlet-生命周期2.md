---
title: Servlet-生命周期(注解的使用)
tags: 
- Servlet-生命周期
categories: 
- Servlet
---

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

