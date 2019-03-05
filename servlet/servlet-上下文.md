---
title: javaweb-上下文
tags: 
- javaweb-上下文
categories: 
- Servlet
---

# 上下文参数

要想配置全局的参数，可以使用< context-param >标签
```xml
<context-param> 
    <param-name>allowed fil type></param-name>
    <param-value>.git, .jpg, .bmp</param-value>
</context-param>
```

在java中获取全局上下文，可以使用
```java
// 属于servlet的方法
getServletConfig().getServletContext();
```