---
title: Servlet-表单信息
tags: 
- Servlet-表单信息
categories: 
- Servlet
---

# Servlet-表单信息


## GET方式提交的表单

URL：路径 + "?",各变量以 & 相连

可能需要设置编码方式
```java
req.setCharacterEncoding("utf-8");
```

获取各变量以 & 相连的部分
```java
request.getQueryString()
```

获取表单信息
```java
request.getParameter("name");
```

## POST方式提交的表单

URL: 不会变化

该方法只返回null
```java
request.getQueryString()
```

获取表单信息
```java
//获取单个参数
request.getParameter("name");
//获取同名多个参数，返回数组
request.getParameters("like");
```

