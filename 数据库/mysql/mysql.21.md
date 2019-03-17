---
title: 更新数据UPDATE
tags: 
- MySQL
- 数据库
categories: 
- Database
---


# 更新数据UPDATE

---

注意  
不要省略WHERE子句，因为稍不注意，可能更新表中所有行

---

## 更新数据
```java
UPDATE <table-name> SET 
    <column-name>=<value>,
    <column-name2>=<value2>,
    <column-name3>=<value3>,
    <column-name4>=<value4>,
    ...
    WHERE 条件;

//忽略错误，继续更新(UPDATE在更新时如果出现错误，整个操作都会被取消)
UPDATE INGNORE <table-name> SET 
    <column-name>=<value>,
    <column-name2>=<value2>,
    <column-name3>=<value3>,
    <column-name4>=<value4>,
    ...
    WHERE 条件;

//删除某个列的值,可以将其设置为NULL
<column-name>=NULL
```