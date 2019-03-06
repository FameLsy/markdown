---
title: Servlet-session-如果Cookie被禁用
tags: 
- Servlet-session-如果Cookie被禁用
categories: 
- Servlet
---

# Servlet-session-如果Cookie被禁用

当某些浏览器并不支持Cookie或者Cookie被禁用的话,想要使用Session,需要进行URL地址重写
1. 原理是将Session的id信息写到URL地址中
2. 服务器通过解析URL获取Session的id

假设由如下URL
```
www.lalaal.com?name=li&age=18
```
通过以下方法可以实现URL地址重
```java
response.encodeURL("www.lalaal.com?name=li&age=18");
```

该方法会自动判断浏览器支不支持Cookie,如果不支持,则将Seesino的id重写到URL中,此时,URL可能变为
```
www.lalaal.com;jsessionid=0UIJHGIGGIOGIUGHJLJIUGI?name=li&age=18
```

如果是页面重定向,则使用以下方法(效果与encodeURL()方法相同)
```java
response.sendRedirect(repsonse.encodeRedirectURL("www.lalaal.com?name=li&age=18"))
```

# Session手动禁止使用Cookie

JavaWeb通过配置来禁止Cookie,具体步骤为
1. 打开META-INF(没有则自己创,该目录与WEB-INF同级)
2. 打开context.xml(没有自己创),输入以下内容

```xml
<?xml version='1.0' encoding='UTF-8'>
<Context path='/mywebpapp' cookie='false' />
```

也可以修改tomcat全局配置文件
```xml
<!-- conf/context.xml -->
<Context cookie='false' />
```

> 注意  
> 改配置只是禁止Session使用Cookie作为标识,斌不能阻止其他Cookie的读写
