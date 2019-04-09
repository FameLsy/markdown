---
title: 删除数据DELETE
tags: 
- MySQL
- 数据库
categories: 
- Database
---


# 删除数据DELETE
---

*注意
- 不要省略WHERE子句，因为稍不注意，可能删除表中所有行
- DELETE删除的是整行。想要删除列，还是要用UPDATE

---

# 删除行

```java
DELETE FROM <table-name> WHERE 条件;
//删除所有行
DELETE FROM <table-name>;
```

# 更快的全部删除

```
TRUNCATE TABLE <table-name>
```

区别：
-TRUNCATE实际上是删除原表重新创建，而不是逐行删除,清空AUTO_INCREMENT记录数
- DELETE是一条一条删除，不清空AUTO_INCREMENT记录数


## 更新和删除所遵循的习惯
- 除非打算更新和删除所有行。否则不要用不带WHERE的UPDATE、DELETE语句
- 保证每个表都有逐渐，尽可能像`WHERE子句那样使用它
- 对UPDATE和DELETE语句使用WHERE子句前，先使用SELECT进行测试，保证正确的记录。
- 使用强制实施完整性的数据库，这样MySQL将不允许删除具有与其他表相关联的数据的行。
- MySQL没有撤销按钮，所以要小心使用UPDATE和DELETE