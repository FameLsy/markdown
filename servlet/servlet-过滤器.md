---
title: Servlet-过滤器-简介
tags: 
- Servlet-过滤器-简介
categories: 
- Servlet
---

# 过滤器(Filter)

过滤器: 
- 用于在Servlet之外对request和response进行修改

滤镜链(FilterChain):
1. 包含多个过滤器
2. request抵达Servlet之前会先经过滤镜链中所有的过滤器
3. response从Servlet传送到客户端浏览器之前也会先经过滤镜链中所有的过滤器

Filter处理过程:

![filter](https://raw.githubusercontent.com/FameLsy/Images/master/servlet/filter.png)