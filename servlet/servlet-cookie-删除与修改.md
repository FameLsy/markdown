---
title: Cookie-删除与修改
tags: 
- Cookie-删除与修改
categories: 
- Servlet
---

# Cookie-删除与修改

Cookie并不提供删除和修改
1. 想要修改，只需要新建一个同名的Cooike，并添加到response中覆盖原有的Cooike
2. 想要删除，只需仙剑一个同名的Cookie，将其maxAge属性设置为0，并添加到response中覆盖原有的Cooike
3. 无论是修改还是Cookie，新建的Cookie除value、maxAge属性外，其他属性要一致。否则浏览器会将其视为不同的Cookie


