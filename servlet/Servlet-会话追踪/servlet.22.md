---
title: Servlet-session-生命周期
tags: 
- Servlet-session-生命周期
categories: 
- Servlet
---

# Servlet-session-生命周期

生命周期
1. Session在客户端第一次请求时创建,需要注意的是只有在访问jsp,servlet等程序时才会创建(JSP底层时通过Servlet实现的),只访问HTML等静态资源时不会产生Session的;(为了获取高速度,Session一般保存在内存中)
2. 当用户持续访问,服务器每次都会刷新Session的最后访问时间
3. 用户每次访问服务器,服务器就会认为该用户的Session活跃了一次
4. 如果Session长时间不活跃,服务器会从内存中删除Session
