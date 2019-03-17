---
title: 视图（MySQL5 ）
tags: 
- MySQL
- 数据库
categories: 
- Database
---


# 视图（MySQL5 ）

什么是视图
- 将一个SELECT的查询结果包装成一个虚拟的表
- 本身不包含数据，仅仅用来查看存储在别处的数据的一种设施。

视图的好处：
- 重复SQL语句
- 鸡蛋花复杂的SQL操作。在编写查询后，可以方便地重用它而不必知道它的基本查询细节
- 使用表的组成部分而不是整个表
- 保护数据，可以给用户授予表的特定部分的访问权限而不是整个表的访问权限
- 更改数据格式和表示。视图可返回与底层表的表示和格式不同的数据。

规则和限制
- 与表一样，视图名唯一
- 对于可创建的视图数目没有限制
- 为了创建视图，必须具有足够的访问权限
- 可以嵌套，即可以利用从其他视图中检索数据的查询来构造一个视图
- ORDER BY可以用在视图中。但如果从该视图检索数据SELECT中也含有ORDER BY,那么视图中的ORDER BY被覆盖。
- 视图不能索引，也不能由关联的触发器或默认值
- 视图可以和表一起使用。例如编写一条联结表和视图的SELECT语句


创建视图
```
CREATE VIEW 视图名
```
示例：
```
CREATE VIEW prdouccustomers AS
SELECT  cust_name, cust_contact, prod_id
FROM customers, orders, orderitems
WHERE customers.cust_id = orders.cust_id
AND orderitems.order_num = orders.order_num
```

查看创建视图的语句
```
SHOW CREATE VIEW viewname
```

更新视图
- 方式一：先DROP在CREATE
- 方式二：不存在则创建，存在就覆盖
```
CREATE OR REPLACE VIEW
```


 个人理解：
- 创建完视图后，发现通过SHOW TABLES可以查看到视图（暂且理解为视图其实算是一种特殊的表）
- 通过SHOW CREATE TABLE 视图,也可以显示视图的创造语句（更加觉得视图是一种特殊的表）
- 在实际使用中，可以当表来进行操作(WHERE，ORDER BY等)
- 虽然视图也可以做到更新（改动基表），但视图本身是用于检索，所以想要更新数据，还是直接操作基表比较好