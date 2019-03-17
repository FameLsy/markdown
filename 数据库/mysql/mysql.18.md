---
title: 组合查询 UNION
tags: 
- MySQL
- 数据库
categories: 
- Database
---


# 组合查询 UNION

使用组合查询的两种情况
- 在单个查询中从不同的表返回类似结构的数据
- 对单个表执行多个查询，按单个查询返回数据


 创建组合查询:如，通过UNION连接两个SELECT
```
SELECT vend_id, prod_id, prod_price
FROM products
WHERE prod_price <=5
UNION
SELECT vend_id, prod_id, prod_price
FROM products
WHERE vend_id IN(1001, 1002)
```

UNION规则
- UNION必须由两条或以上SELECT语句组成，语句之间用UNION分割
- UNION中的每个查询必须包含相同列、表达式或聚集函数（次序可以不同）
- 列数据列下必须兼容。（可不必完全相同，但必须隐含转换的类型）

包含或取消重复的行
- UNION：自动去除重复的行
- UNION ALLL：返回全部的行
 
对组合查询结果排序
- 在最后一个SELECT后面加ORDER BY，将排序所有SELECT语句返回的结果。