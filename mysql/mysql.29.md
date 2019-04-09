---
title: 用户相关
tags: 
- MySQL
- 数据库
categories: 
- Database
---





# 用户相关

日常开发不应使用root用户

获取所有账号列表
- 系统的 mysql 数据库存放了用户信息表 user 
```
USE mysql;
SELECT user FORM user;
```

创建用户
```
CREATE USER 用户名 
```

创建用户并指定密码
```
CREATE USER 用户名 IDENTIFIED BY '密码' 
```

重命名
```
 RENAME USER 原名 TO 更改名 
```

设置/更新密码
```
 SET PASSWORD 
```
示例1 设置指定用户密码， Password（） 函数加密
```
SET PASSWORD FOR famel = Password('123456')
```
示例2 设置当前登录用户
```
SET PASSWORD =Password('123456')
```

删除账号 
```
DROP USER 用户名 
```


查看用户权限(新用户没有任何权限，只能登录MySQL)
``` 
SHOW GRANTS FOR 用户名
``` 

授权 
```
GRANT 权限 ON 数据库.表 TO 用户 
```
示例
```java
//赋予了 famle 用户关于数据库 crashcourse 下所有表的 SELECT 权限
 
GRANT SELECT ON crashcourse.* TO famel
 ```

授权多次,权限用 "," 分隔
``` 
GRANT SELECT, INSERT ON database.table TO famel;
```

删除权限
```
REVOKE 权限 ON 表 FROM 用户 
```

GRENT 和 REVOKE 可以在几个层次上控制访问权限
- 整个服务器， GRANT ALL 和 REVOKE ALL 
- 整个数据库, ON database.* 
- 特定表, ON database.table 
- 特定列
- 特定存储过程
 

可用权限

|权 限| 说 明|
|--|--|
| ALL| 除GRANT OPTION外的所有权限|
| ALTER|使用ALTER TABLE |
| ALTER ROUTINE|使用ALTER PROCEDURE|DROP PROCEDURE |
|CREATE | 使用CREATE TABLE|
|CREATE ROUTINE |使用CREATE PROCEDURE |
| CREATE TEMPORARYTABLES|使用CREATE TEMPORARY TABLE |
|CREATE USER |使用CREATE USER、DROP USER、RENAME USER和REVOKE
ALL PRIVILEGES|
|CREATE VIEW | 使用CREATE VIEW|
|DELETE | 使用DELETE|
| DROP| 使用DROP TABLE|
| EXECUTE|使用CALL和存储过程 |
| FILE| 使用SELECT INTO OUTFILE和LOAD DATA INFILE|
| GRANT OPTION| 使用GRANT和REVOKE|
|INDEX |使用CREATE INDEX和DROP INDEX |
|INSERT | 使用INSERT|
|LOCK TABLES |使用LOCK TABLES |
|PROCESS | 使用SHOW FULL PROCESSLIST|
|RELOAD |使用FLUSH |
| REPLICATION CLIENT|服务器位置的访问 |
|REPLICATION SLAVE |由复制从属使用 |
|SELECT |使用SELECT |
| SHOW DATABASES|使用SHOW DATABASES |
| SHOW VIEW| 使用SHOW CREATE VIEW|
|SHUTDOWN | 使用mysqladmin shutdown（用来关闭MySQL）|
| SUPER| 使用CHANGE MASTER、KILL、LOGS、PURGE、MASTER
和SET GLOBAL。还允许mysqladmin调试登录|
|UPDATE |使用UPDATE |
|USAGE | 无访问权限|