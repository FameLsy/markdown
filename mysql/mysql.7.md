---
title: SELECT子句-LIMIT
tags: 
- MySQL
- 数据库
categories: 
- Database
---

# LIMIT

MySQL特有的语法，实现了物理分页

物理分页：
- 直接在数据库中进行分页，最终只返回分页后的数据


```java
//offset:起始位置
//rows:每页多少行
SELECT * FROM <table-name> LIMIT [offset,] rows;
//另一种写法
SELECT * FROM <table-name> LIMIT rows OFFSET [offset];
```

示例
```java
//从第2位置开始返回3行(（第一行为0)：
LIMIT 2,3
```
