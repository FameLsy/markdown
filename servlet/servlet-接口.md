---
title: Servlet 接口
tags: 
- Servlet 接口
categories: 
- Servlet
---

# Servlet 接口

Servlet 是一个，位于javax.servlet.Servlet

Servlet规范是建立在HTTP规范基础上，HTTP 1.1规范支持OPTIONS、GET、POST、HEAD、PUT、DELETE以及TRACE等7中Web访问方式,只需要了解POST和GET即可

GET: 表示查询信息，URL中可以附带少量信息，URL总长度不超过255个字符，且参数会显示在浏览器地址栏

POST: 表示提交信息，一般用于大数据信息或者文件，长度不受限制，且参数不会显示在浏览器地址栏

当浏览器以POST/GET方式访问网络程序，Servlet会执行如下方法
```java
// 对于GET方式
void doGet(HttpServletRequest request, HttpServletResponse response)
// 对于POST方式
void doPost(HttpServletRequest request, HttpServletResponse response)
```

此外，如下方法可以返回该文档的最后修改时间，默认为-1
```java
long getLastModified(HttpServletRequest request)
```
