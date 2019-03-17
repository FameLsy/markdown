---
title: SELECT子句-WHERE
tags: 
- MySQL
- 数据库
categories: 
- Database
---


# 过滤数据（位于ORDER BY之前）


## 指定单个搜索条件
```java
//WHERE 条件,条件由<column-name>、运算符、数据组成
WHERE <column-name> 运算符 数据
```

## WHERE 可用条件

比较运算符：

|运算符|描述|
|--|--|
|>, <, <=, >=, =, <> |大于、小于、小于等于、大于等于、等于，不等于|
|BETWEEN...AND|某区间的值（包含头尾）|
|IN()|指定IN()中的特定值，如IN(100,200)|
|LIKE ''| 模糊查询|
|IS NULL/IS NOT NULL|判空|

逻辑运算符：
|运算符|描述|
|--|--|
|AND|多条件同时成立|
|OR|多条件任一成立,优先级低于AND，可用()来提升优先级|
|NOT|否定其后的条,mysql仅支持对IN、BETWEEN、EXISTS句子取反|

---

注：
- 字符串类型用单引号''括起来
- 值与字符串类型比较时，字符串会转为值。系统采取截取方式，截取字符串前面的数值，如02asda那么就是02,而对于字母开头的,就直接转为0。

---

# 通配操作符LIKE

% 通配符：匹配0、1或多个字符

```java
//找出所有以jet开头的数据
WHERE prod_name LIKE 'jet%';
```

_ 通配符：匹配单个字符
```java
//将会匹配1 ton anvil,而不会匹配 .5 ton anvi 
WHERE prod_name LIKE '_ ton  anvil'
```

---

注: 
- 尾空格会干扰通配符匹配。如anvil后多了一个或多个空格。"%anvil "将会不匹配到它，解决方法是在后面也加一个%. %anvil% 或使用函数去除。
- %无法匹配NULL值，即"LIKE '%'"不会匹配到值NULL的行。
- 不要过度使用通配符，能使用其他操作符就使用其他操作符
- 通配符置于开始，搜索最慢（即尽量不要使用在开头）

---