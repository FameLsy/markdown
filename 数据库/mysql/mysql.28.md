---
title: 字符集
tags: 
- MySQL
- 数据库
categories: 
- Database
---


# 字符集
重要术语
- 字符集：字母和符号的集合
- 编码：某个字符集成员的内部表示
- 校对：规定字符如何比较的指令

查看Mysql支持的字符集完整列表
- 显示所有可用的字符集以及每个字符集的描述和默认校对

```
SHOW CHARACTER SET
```


查看Mysql支持的校对完整列表
```
SHOW COLLATION
```

查看数据库默认的字符集
```
SHOW VARIABLES LIKE 'character%'
```

查看数据库默认的校对：
```
SHOW VARIABLES LIKE 'collation%'
```

在创建表时指定字符集和校对
```
CREATE TABLE mytable(
	column1 INT,
	column2 VARCHAR(10)
) DEFAULT CHARACTER SET hebrew
   COLLATE hebrew_general_ci;
```

MySQL使用字符集和校对规则
- 明确指定两个
- 只指定CHARACTER SET，则使用此字符集及其默认的校对
- 都不知道，则使用数据库默认

 列级别的字符和校对
 ```
 CRETE TABLE mytable(
 column3 VARCHAR(10) CHARACTER SET latin1 COLLATE latin1_general_ci
 ) DEFAULT CHARACTER ser HEBREW
   COLLATRE hebrew_general_ci
 ``