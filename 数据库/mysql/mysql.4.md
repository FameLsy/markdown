---
title: 表相关
tags: 
- MySQL
- 数据库
categories: 
- Database
---

# 表相关

## 创建表
```java
CREATE TABLE <table-name> (
	<column-name> 类型(长度) 约束,
	<column-name> 类型(长度) 约束
);

// 不存在则创建
CREATE TABLE IF NOT EXISTS <table-name>(
	<column-name> 类型(长度) 约束,
	<column-name> 类型(长度) 约束
)
```

---

约束
- NULL或NOT NULL:在定义列时加入这两个关键字，表示允许NULL或不允许NULL,默认为NULL
- PRIMARY KEY (column1,column2 ): 定义主键/组合主键
- AUTO_INCREMENT:  自动增量,每次执行INSERT，自动对该列增量，赋值下一个可用的值。可以在INSERT语句中插入指定值，只要是没有使用的，该值回代替自动生成的值。
- DEFAULT < value >:指定默认值

---

## 查看当前数据库所有表:

```
SHOW TABLES;
```

## 显示创建表语句：

```
SHOW CREATE TABLE <table-name>
```

## 查看表列(结构)：
```
SHOW COLUMNS FROM <table-name>
```

## 查看表列另一种方式(效果一样)：

```
DESCRIBE <table-name>
```

## 查看自动增量的值

(该函数回获取最后一个AUTO_INCREATEMENT的值)

```
SELECT last_insert_id()
```

## 修改表

添加列
```
ALTER TABLE <table-name> ADD <column-name> 类型(长度) 约束; 
```

修改类类型、长度、约束
```
ALTER TABLE <table-name> MODIFY <column-name> 类型(长度) 约束; 
```

修改列名
```
ALTER TABLE <table-name> CHANGE <old-column-name> <new-column-name> 类型(长度) 约束; 
```

删除列 
```
ALTER TABLE <table-name> DROP <column-name>
```

修改表名
```
RENAME TABLE <old-table-name> TO <new-table-name>
```

修改表的字符集
```
ALTER TABLE <table-name> CHARACTER SET 字符集;
```





## 定义外键
```
ALTER TABLE <table-name>
	ADD CONSTRAINT <约束名称>
	FOREIGN KEY(主表外键字段)
	REFERENCES 从表(从表主键)
```
示例：
```java
//外键属性
//- fk_orderitems_orders：外键字段的名字
//- FOREIGN KEY(order_num)：当前表的外键字段
//- REFERENCES orders(order_num)：对应的表的字段(orders表的order_num字段)

ALTER TABLE orderitems
ADD CONSTRAINT fk_orderitems_orders
FOREIGN KEY(order_num) 
REFERENCES orders(order_num)
```

## 删除外键
```
ALTER TABLE DROP FOREIGN KEY 外键字段名
```
