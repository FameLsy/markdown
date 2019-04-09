---
title: SELECT语句-简介
tags: 
- MySQL
- 数据库
categories: 
- Database
---
# SELECT 


## 查询语法
- 通过DISTINCT关键字，将SELECT检索的结果，只返回不同值
```
SELECT [DISTINCT] <column-name>,<column-name>,... FROM <table-name> 
[WHERE 条件]
[GROUP BY]
[HAVING]
[ORDER BY]
[LIMIT]
``` 

## 别名查询
```java
//单列别名
SELECT  <column-name>,<column-name> AS <alias-name>,... FROM <table-name> 
//多列别名
SELECT  (<column-name>,<column-name>) AS <alias-name>,... FROM <table-name> 
//表别名
SELECT  <column-name>,<column-name> ,... FROM <table-name> AS <alias-name>
```  

## 运算查询

```
SELECT  <column-name>*10, <column-name>+20 AS <alias-name> ,... FROM <table-name> 
```

## SELECT 子句顺序

|子句|说明|是否必须使用|
|--|--|--|
|SELECT|要返回的列或表达式 |是 |
|FROM|从中检索数据的表|仅在从表选择数据时使用|
|WHERE|行级过滤|否|
|GROUP BY|分组说明|仅在安祖计算聚焦时使用|
|HAVING|组级过滤|否|
|ORDER BY|输出排序顺序|否|
|LIMIT|要检索的行数|否|

