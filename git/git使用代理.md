---
title: git代理使用
tags: 
- git代理使用
categories: 
- git
---

架设了bwg + v2ray,走的SOCKS,配置github代理如下
```xml
Host github.com
   HostName github.com
   User git
   # connect是git安装后自动装的
   ProxyCommand "C:\Program Files\Git\mingw64\bin\connect.exe" -S localhost:1080 %h %p
```