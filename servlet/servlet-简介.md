---
title: Servlet-简介
tags: 
- Servlet
categories: 
- Servlet
---

Servlet 是Java Web的核心程序，所有的网址最终都交给Servlet来处理

# Servlet 工作流程

servlet 工作流程:
1. 浏览器提交的请求是遵循HTTP协议的文本，而这段文本有服务器（以Tomcat为例）Tomcat接受并解析
2. Tomcat将请求封装成了HttpServletRequest类型的request对象。所有HTTP头数据都可以通过request相应的方法查询到
3. Tomcat把输出流封装为HttpServleteResponse类型的response对象，通过设置resonse属性就可以控制输出内容。

![servlet](https://raw.githubusercontent.com/FameLsy/Images/master/servlet/servlet.png)
