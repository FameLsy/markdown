---
title: 索引
tags: 
- MySQL
- 数据库
categories: 
- Database
---


## 简介

简介
- 使用索引的目的时为了优化查询速度
- 索引时一种特殊的文件或者叫数据结构（InnoDB数据表上的索引是表空间的一个组成部分），它包含对数据表里的所有记录的引用指针
- 通俗的讲，索引就相当一数据库的目录

因为索引是在存储引擎中实现，所以不同存储引擎有不同索引
- MyISAM和InnoDB:只支持BTREE索引
- MEMORY/HEAP存储引起：支持HASH和BTREE索引

## 索引分类

索引分类
- 单列索引
- 组合索引

单列索引
- 普通索引：MySQL中基本索引类型，没有限制，允许定义索引的列中插入重复值和空值
- 唯一索引：索引列中的值必须是唯一的，但允许NULL
- 主键索引：特殊唯一索引，不允许NULL

组合索引
- 在表中的多个字段组合上创建的索引，只有在查询条件中使用了这些字段的左边字段时，索引才会被使用，使用组合索引时遵循最左前缀集合。

全文索引
-  全文索引，只有在MyISAM引擎上才能使用，只能在CHAR,VARCHAR,TEXT类型字段上使用全文索引。

此外，还有空间索引，不常用.

## 索引使用

### 创建索引

普通索引的创建

```java
//创建索引
CREATE INDEX <index-name> ON <table-name>(
    <column-name>(length)
)
//另一种方式
ALTER TABLE  <table-name> ADD INDEX <index-name> ON (
    <column-name>(length)
)
```

唯一索引的创建
```java
//创建索引
CREATE UNIQUE INDEX <index-name> ON <table-name>(
    <column-name>(length)
)
```

全文索引创建
```java
//创建索引
CREATE FULLTEXT INDEX <index-name> ON <table-name>(
    <column-name> (length)
)
```

组合索引的创建
```java
//创建索引
CREATE [FULLTEXT/UNIQUE] INDEX <index-name> ON <table-name>(
    <column-name>(length),
    <column-name>(length)
    ...
)
```

### 删除索引
```
DROP INDEX <index-name> ON <table-name>
```