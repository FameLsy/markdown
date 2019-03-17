---
title: mysql命令行使用程序
tags: 
- MySQL
- 数据库
categories: 
- Database
---


## 登录MySQL

mysql [command]

```java
mysql -u root -p 
//指定用户(-u)、密码登录(-p)、主机(-h,默认localhost)、端口(-P,3306)；
mysql -u root -p -h myserver -P 9999
```

## 查看帮助文档

help [command]

```java
//例如查看指令**SELECT**
help SELECT
```

## 退出操作

```java
exit
//或者
quit
```

## 测试服务器
- 查看mysql的一些基本信息：

```java
/usr/bin/mysqladmin -u root -p version
```

- 查看mysql的变量:

```java
/usr/bin/mysqladmin -u root -p variables
```

- 验证是否能关闭mysql:

```java
/usr/bin/mysqladmin -u root -p shutdown
```

- 再次启动Mysql:

```java
/usr/bin/mysqld_safe --user=mysql &
```

- 查看数据库:

```java
/usr/bin/mysqlshow -u root -p
//指定数据库则显示数据库中的表
/usr/bin/mysqlshow -u root -p  test
```

- 查看表中主句:

```java
/usr/bin/mysql -e "SELECT prod_name FROM products" -u root -p test
```

- 显示服务器状态：

```
service mysql status
```

## 导入sql文件
命令行模式：

```java
//（指定sql文件的完全路径）
source [${path}.*sql]
```