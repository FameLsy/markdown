---
title: Mybatis环境搭建
tags: 
- Mybatis环境搭建
categories: 
- Mybaits
---

# 下载MyBatis

方式一：通过maven下载

方式二：手动下载

[Mybatis](https://github.com/mybatis/mybatis-3/releases)

解压后可得lib包或者jar包，我们选择jar包导入


```xml
<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.4.6</version>
</dependency>
<!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.25</version>
</dependency>

```
