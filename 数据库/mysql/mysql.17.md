---
title: 联结
tags: 
- MySQL
- 数据库
categories: 
- Database
---


# 联结

当数据存储在多个表中，使用联结方式，在单条SELECT 语句检索出数据。

JOIN按照功能可分为
- CROSS JOIN(交叉连接)
- INNER JOIN(内连接或等值连接)
- OUTER JOIN(外连接)

此外，还有其他连接
- 自连接: 双表都是自身时使用
- 自然联结: 多表连接查询所有列，保证出现重复列

## 交叉连接

又名笛卡儿积连接,检索出的行的数目将是第一个表中的行数乘以第二个表中的行数。

```java
//隐式交叉连接
SELECT * FROM A,B;
//显示交叉连接
SELECT * FROM A CROSS JOIN B;
```

## 内连接或等值连接

内连接使用比较运算符根据每个表共有的列的值匹配两个表中的行

```java
//隐式内连接
SELECT * FROM A,B WHERE A.id = B.id;
//显示内连接
SELECT * FORM A INNER JOIN B ON A.id = B.id;
```

## 外连接

外连接包括
- 左外连接
- 右外连接

此外，外连接需要有主表或者保留表的概念；查询结果会以主表为主，从表记录匹配不到，则补NULL

### 左外连接

其中A为主表
```
SELECT * FROM A LEFT JOIN B ON A.id = B.od;
//或
SELECT * FROM A LEFT OUTER JOIN B ON A.id = B.od;
```

### 右外连接

其中B为主表
```
SELECT * FROM A RIGHT JOIN B ON A.id = B.od;
//或
SELECT * FROM A RIGHT OUTER JOIN B ON A.id = B.od;
```

- 使用表别名(可以省写表名)
```
SELECT cust_name, cust_contact
FROM customers AS c, orders AS o, orderitems AS oi
WHERE c.cust_id = o.cust_id
AND oi.order_num = o.order_num
AND prod_id = 'TNT2'
```

## 自联结

使用情况，如一个货物id为DTNTR,想要查询这个货物对应厂商的其他货物，就需要使用自联结了。（p1、p2实际为一张表）
第一步：查询到id为DTNTR对应的vend_id
第二步：查询到vend_id对应的prod_id, prod_name
```
SELECT p1.prod_id, p1.prod_name
FROM products AS p1, products AS p2
WHERE p1.vend_id = p2.vend_id
AND p2.prod_id = 'DTNTR'
```

## 自然联结
使用情况，在对表进行联结时，肯定会有至少一个列的值时重复的。而自然联结，就是排除多次出现的列。使每个列只返回一次。
具体做法
- 对一张表使用SELECT *
- 其他表的列使用明确的子集

```
SELECT c.*, 
		o.order_num,
		o.order_date,
		oi.prod_id, 
		oi.quantity, 
		oi.item_price
FROM customers AS c, orders AS o, orderitems AS oi
WHERE c.cust_id = o.cust_id
AND oi.order_num = o.order_num
AND prod_id = 'FB'
```
