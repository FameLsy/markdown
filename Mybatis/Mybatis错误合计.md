---
title: Mybatis错误合集
date: 2019-02-16 00:00:21
tags: 
- Mybatis错误合集
categories: 
- error
---

# 关键字 不允许有匹配 "[xX][mM][lL]" 的处理指令目标。
原因：xml配置文件错误
```
# 具体为，xml必须如下开头，且前面不能有空格
<?xml version="1.0" encoding="UTF-8" ?>
```

# 关键字 The server time zone + 乱码
原因：数据库和系统时区差异
```
# 加serverTimezone=GMT%2B8 ,GMT%2B8代表东八区
url = "jdbc:mysql://localhost:3306/mydb?serverTimezone=GMT%2B8"
```