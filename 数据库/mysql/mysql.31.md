---
title: 备份
tags: 
- MySQL
- 数据库
categories: 
- Database
---


# 备份
 mysqldump
- 转储所有数据库内容到某个外部文件。在进行常规备份前这个实用程序应该正常运行，以便能正确地备份转储文件。
mysqlhotcopy
- 从一个数据库复制所有数据（并非所有数据库引擎都支持这个实用程序）。

BACKUP TABLE或SELECT INTO OUTFILE
- 转储所有数据到某个外部文件。这两条语句都接受将要创建的系统文件名，此系统文件必须不存在，否则会出错
- 数据可以用RESTORE TABLE来复原。

FLUSH TABLES
- 为了保证所有数据被写到磁盘（包括索引数据），可能需要在进行备份前使用