---
title: Servlet-重定向与跳转
tags: 
- Servlet-重定向与跳转
categories: 
- Servlet
---

# Servlet-重定向与跳转

## 跳转(forword)

跳转是通过RequestDispathcer对象的forward(aRequest, aResponse)方法来是实现的

实现一个简单的Serlvet跳转到另一个Serlvet
```java
RequestDispatcher dispatcher =
    request.getRequestDispatcher("/servlet/HelloServlet");
dispatcher.forward(request,response);
```

>注：  
>getRequestDispatcher()参数必须以"/"开始，表示Web根目录

此外，forword()跳转不止这一种
1. Servlet跳转到Servlet
2. Servlet跳转到页面
3. Servlet跳转到文件(包括WEB-INF下的文件)
4. 只要getRequestDispatcher()的参数路径不同，就可以跳转

## 重定向(Redirect):

重定向是利用服务器返回的状态码来实现的。当客户掉浏览器请求服务器时，服务器端会返回一个状态码

设置状态码
```java
response.setStatus(int stauss)
```

状态码解释

|状态码|描述|
|--|--|
|1xx|信息状态码，表示请求已经被接受，正在处理|
|2xx|正确状态码，表示请求已被正确接受并处理，没有错误发生,如200(表示一切正常）|
|3xx|重定向状态码，如301（资源永久换位置,永久重定向）；302（资源临时换了地址,临时性重定向），通常服务器会发送HTTP Location来重定向到正确的新位置|
|4xx|请求错误状态码，如401(无权访问)，404(无资源)， 405(访问方式错误，如Servlet只接受Post访问)|
|5xx|服务器错误状态码，如500(程序出现异常而中途停止运行)|




