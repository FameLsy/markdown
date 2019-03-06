---
title: session-cookie-对比
tags: 
- session-cookie-对比
categories: 
- Servlet
---

# 简介

Session对比cookie
1. 存储方式不同:  
    Cookie只能保存ASCII字符,如果要存储Unicode或二进制数据,需要进行编码;  
    Session可以存取任何类型数据,从基本类到Java类都没有问题  
2. 存储位置不同:  
    Cookie存放客户端浏览器中  
    Session存放在服务器中  
3. 隐私安全不同:  
    Cookie是对客户端可见的,恶意程序可能会复制,修改Cookie的内容;  
    Session是服务器可见的,对客户端透明(所以Cookie不宜保存敏感信息,如账号密码)  
4. 有效期不同:  
    Cookie可以通过setMaxAge(Integet.MAX_VALUE)来达到永久记录信息;  
    Session无法永久记录信息,因为其依赖于Cookie,而该Cookie又是maxAge=-1,只要关闭了浏览器Session就会失效,且设置过长时间的Seesion会导致内存溢出  
5. 对服务器负担不同:  
    Cookie是保存在客户端的,不占用服务器资源;  
    Session保存在服务器内存中,如果访问用户多,会造成产生大量Session,消耗服务器大量内存  
6. 浏览器支持不同:  
        Cookie一旦被禁用,就会失效;Cookie可以设置成本浏览器窗口和子窗口内有效(maxAge=-1),或所有浏览器窗口有效(maxAge>0);  
        Session只能在本浏览器窗口和子窗口有效;Session在Cookie被禁用的情况下,可以使用URL地址重写方式生效;  
7. 跨域名不同:  
    Cookie支持跨域名访问(通过domain属性)
    Session只能在它所在的域名有效

