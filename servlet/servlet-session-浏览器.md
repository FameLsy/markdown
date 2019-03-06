---
title: Servlet-session-浏览器
tags: 
- Servlet-session-浏览器
categories: 
- Servlet
---

# Servlet-session-浏览器

从浏览器到服务器来讲,Session需要浏览器的支持
1. 因为HTTP的无状态性,Session需要通过Cookie来作为标识
2. 服务器会自动向浏览器发送一个名为JSESSIONID的Cookie,它的值为该Session的id
3. 该Cookie的maxAge为-1,仅在当前浏览器有效,且不同窗口不共享

>注意  
>新开的窗口会生成新的Session,但子窗口除外  
>子窗口会公用父窗口的Session(右键->在新窗口打开,就是子窗口)  

