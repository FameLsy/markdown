---
title: Servlet-cookie-简介
tags: 
- Servlet-cookie-简介
categories: 
- Servlet
---

# Cookie

cookie工作原理  
1. 为客户端配置一个"通行证"，通过通行证来确认身份(本质上，cookie是一小段文本)
2. 客户端请求服务器，服务器需要记录该用户的状态，则使用response向浏览器发送一个Cookie，客户端保存该Cookie
3. 客户端再次请求时，连同cookie一起发送(浏览器只会提交Cookie的name和value)，服务器根据cookie的值来判断用户状态
在浏览器控制台输入以下命令，可查看当前cookie
```js
javascript:alert(document.cookie)
```

浏览器会将Cookie保存到本地电脑，如chrome的保存地址为
```
C:\Users\57560\AppData\Local\Google\Chrome\User Data\Default\Cookies
```

在java中，cookie被封装成了javax.servlet.http.Cookie类

获取cookie
```java
[Cookiep[]] request.getCookie();
```

设置cookie
```java
response.addCookie(Cookie cookie)
```

此外，cookie的保存形式为key-value方式



