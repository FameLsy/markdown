---
title: Cookie-域名
tags: 
- Cookie-域名
categories: 
- Servlet
---

# Cookie的不可跨域名性

1. Cookie在客户端由浏览器管理，它能保证A网站的Cookie只能由A网站使用，B网站无法使用，这就是不可跨域性
2. 浏览器保证不可跨域名性是通过域名
3. 正常情况下，即使是一级域名下的两个二级域名(如code.baidu.com、images.baidu.com),也是不共享Cookie的

# Cookie设置共享

使用的是Cookie的domain属性
```
//所有baidu.com名下的二级域名都可以使用该Cookie
aCookie.setDomain(".baidu.com");
```

>注意:  
>domain参数必须以 . 开始

想要为两个完全不同的域名设置共享Cookie，方式为
1. 生成两个Cookie
2. 生成的Cookie通过设置domain分别将对方域名添加进去