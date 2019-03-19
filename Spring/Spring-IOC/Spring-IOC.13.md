<!-- ---
title: Scope注解
tags: 
- Scope注解
categories: 
- Spring 
--- -->

# @Scope注解

效果相当于
```xml
< bean id="" class="" scope="">
```

用法如下
```java
@Scope(value="singleton")
```

>scope的作用范围  
>singleton :默认值，单例的（在整个容器中只有一个对象  
>prototype :多例  
>request:Web 项目中,Spring 创建一个 Bean 的对象,将对象存入到 request 域中  
>session:Web 项目中,Spring 创建一个 Bean 的对象,将对象存入到 session 域中  
>global session	:Web 项目中,应用在 Portlet 环境.如果没有 Portlet 环境那么globalSession 相当于 session.
