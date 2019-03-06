---
title: Servlet-会话追踪
tags: 
- Servlet-会话追踪
categories: 
- Servlet
---

# 简介

会话(Session) 用来跟踪用户的整个会话，常用技术为
1. session
2. cooike

HTTP协议的无状态:  
简单来说就是WEB使用HTTP传输数据时，一旦数据交换完毕，客户端与服务器端的连接关闭，当再次交换数据时就需要重新建立连接

会话追踪的出现，就是为了解决HTTP协议无状态的问题