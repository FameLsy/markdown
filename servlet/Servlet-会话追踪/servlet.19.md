---
title: Cookie-安全属性
tags: 
- Cookie-安全属性
categories: 
- Servlet
---

# Cookie安全属性

HTTP协议不光无状态，还不安全。使用HTTP协议的数据不经过任何加密就直接在网络上传播，有被截获的可能

不希望Cookie在HTTP等非安全协议中传输，可以设置secure属性
```java
//浏览器将只会在HTTPS, SSL等安全协议中传输此Cookie
aCookie.setSecure(true);
```

>注意  
> secure属性并不是对Cookie加密,想要更高的安全性,还是需要对Cookie内容进行加密,解密

