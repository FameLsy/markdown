---
title: 插入数据 INSERT
tags: 
- MySQL
- 数据库
categories: 
- Database
---


# 插入数据 INSERT

## 插入完整的行
- 列可以不安次序，但值与列次序一一对应，没有值的使用NULL值
- 可以省略某些列，但该列必须允许NULL值或在表定义中给出了默认值
- 提高性能：INSERT INTO耗时，且可能降低等待处理的SELECT语句的性能。如果数据检索是最重要的，可以使用INSERT LOW_PRIORITY INTO来降低INSERT语句的优先级(同样适用UPDATE和DELETE)

```
INSERT INTO <table-name>(
	<column-name>,
	<column-name2>,
	<column-name3>，
	...
	) VALUES(
		<value>,
		<value>,
		<value>,
		...
	)
```

## 插入多个行

每组值用一堆圆括号括起来，用逗号分隔
```
INSERT INTO <table-name>(
	<column-name>,
	<column-name2>,
	<column-name3>，
	...
)
VALUES(
	<value>,
	<value>,
	<value>,
	...
),
(
	<value>,
	<value>,
	<value>,
	...
),
...

```

## 插入检索出的数据: 
```
INSERT INTO <table-name> (
	<column-name>,
	<column-name2>,
	<column-name3>，
	...
) SELECT  * FROM <table>
```
