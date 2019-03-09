---
title: Servlet-表单信息
tags: 
- Servlet-表单信息
categories: 
- Servlet
---

# javaweb-资源

## 配置资源
对于一些资源，可以在web.xml使用标签< env-entry >来配置（该标签只能配置java.lang包下的标准类型变量，即Stirng和八种基本变量）

```xml
    <env-entry>
        <env-entry-name>name</env-entry-name>
        <env-entry-type>java.lang.String</env-entry-type>
        <env-entry-value>liisyu</env-entry-value>
    </env-entry>


```

## 注入资源

Servlet是如何获取web.xml的初始参数的？  

在java5.0后，引入了@Resource注解，可以帮我们将参数注射到Servlet类中(可注入java bean 、String、8大基础类)

```java
@Resource(name="name")
private String name;
```

## 使用JDNI获取资源

```java
Context context = new InitialContext();
String name = (String)context.loolup("name");
```