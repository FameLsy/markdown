---
title: 子查询
tags: 
- MySQL
- 数据库
categories: 
- Database
---


# 子查询

又叫内部查询，即嵌套在其他查询中的查询；包含内部查询的被成为外部查询



## 利用子查询过滤

将一条SELECT语句返回的结果用于另一条SELECT语句的WHERE子句
```
SELECT cust_id
FROM orders
WHERE order_num IN (SELECT order_num 
		FROM orderitems 
		WHERE prod_id = 'TNT2')
```

## 作为计算字段使用子查询
```
SELECT cust_name,
		cust_state.
		(SELECT COUNT(*)
		FROM orders
		WHERE orders.cust_id = customers.cust_id) AS orders
FROM customers
ORDER BY cust_name
```