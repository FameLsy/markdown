---
title: Cookie-有效期
tags: 
- Cookie-有效期
categories: 
- Servlet
---

# Cookie-有效期

Cookie的maxAge控制着Cookie的有效期，单位为秒
1. 获取: getMaxAge()
2. 设置: setMaxAge()

maxAge的值
1. 正数：cookie会在${maxAge}秒之后自动消失，浏览器会将maxAge持久化写入到本地Cookies文件中
2. 负数：仅在本浏览器窗口以及本窗口打开的子窗口有效，关闭窗口立即失效(maxAge默认-1)
3. 0：表示立即失效，因为Cookie机制没有提供删除Cookie操作，一般都设置为0，失效的Cookieh会被浏览器从Cooike文件中删除