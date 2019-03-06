---
title: Servlet-请求与响应
tags: 
- Servlet-请求与响应
categories: 
- Servlet
---

# 请求

请求会被封装成HttpServletRequest对象，包含了请求地址、请求参数等所有信息

```java
        //设置request编码方式
        req.setCharacterEncoding("UTF-8");

        //其他req.getxxx方法,太多，看API
```

# 响应

服务器对浏览器的响应会被封装成HttpServletResponse对象，要对浏览器操作，只需操作该对象
```java
        //设置response的编码方式
        resp.setCharacterEncoding("UTF-8");

        //设置文档类型为HTML类型
        resp.setContentType("text/html");

         //设置文档类型为HTML类型
        resp.setContentType("image/jep");

        //设置文档类型为Word
        resp.setContentType("application/msword");

        //获取字符型输出流，输出到浏览器
        PrintWriter writer = resp.getWriter();

        //二进制输出流，比方说输出图片
        ServletOutputStream outputStream = resp.getOutputStream();

        //输出
        writer.flush();
        outputStream.flush();
```