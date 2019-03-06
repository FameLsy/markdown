---
title: Servlet-session-简介
tags: 
- Servlet-session-简介
categories: 
- Servlet
---

# 简介

Session除Cookie外的另一种记录客户状态的机制,当客户端访问服务器时,服务器会将客户端的信息以session的形式保存到服务器.客户端再次访问时,只需要从Session中寻找该客户的状态即可

从浏览器到服务器来讲,Session需要浏览器的支持
1. 因为HTTP的无状态性,Session需要通过Cookie来作为标识
2. 服务器会自动向浏览器发送一个名为JSESSIONID的Cookie,它的值为该Session的id

Session 对应类为javax.servlet.http.HttpSession,属于key-value存储方式

获取Session
```java
// Session 不存在返回null
request.getSession();
// Session不存在,如果aBoolean=true,新建一个Session再返回
request.getSession(aBoolean);
```

设置/获取session属性
```java
aSession.setAttribute(key, value);
aSession. getAttribute(key);
```

session常用方法

![session](https://raw.githubusercontent.com/FameLsy/Images/master/servlet/session.png)
