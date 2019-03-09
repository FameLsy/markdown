---
title: Cookie-编码
tags: 
- Cookie-编码
categories: 
- Servlet
---

# Cookie-编码

对于编码，中文属于Unicode字符，占据4个字符，而英文数以ASCII字符，占据2个字符。

Cookie中要使用中文，可以使用java.net.URLEncoder类进行编码
```java
new Cookie(
    URLEncoder.encode("姓名", "UTF-8"),
    URLEncoder.encode("张三", "UTF-8")
    )
```

对于解码，则使用java.net.URLDecoder类
```java
URLDecoder.decode(aCookie.getName(), "UTF-8")
URLDecoder.decode(aCookie.getValue(), "UTF-8")
```

