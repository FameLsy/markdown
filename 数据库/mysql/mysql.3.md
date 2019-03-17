---
title: 数据库相关
tags: 
- MySQL
- 数据库
categories: 
- Database
---



# 数据库相关

## 创建数据库
```
CREATE DATABASE <database-name> [CHARACTER SET 字符集]
```

## 使用数据库：

```java
USE <database-name>
```

## 查看所有数据库：

```java
SHOW DATABASES;
```

## 显示创建数据库语句：
```
SHOW CREATE DATABASE <Databasee-name>
```

## 删除数据库

```
DROP DATABASE <database-name>
```

## 查看正在使用的数据库
```
SELECT database()
```
