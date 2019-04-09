---
title: LIKE与RNGEXP的不同
tags: 
- MySQL
- 数据库
categories: 
- Database
---

如：
```java
//匹配整个列的值，即要完全匹配
LIKE '1000'
//该值只要在列中出现，则匹配
REGEXP '1000'
//想要匹配整个列
REGEXP '^1000$'
```
