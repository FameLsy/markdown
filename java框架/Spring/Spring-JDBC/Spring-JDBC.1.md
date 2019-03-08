---
title: Spring-JDBC-依赖
tgas: 
- Spring-JDBC-依赖
categories: 
- Spring 
---

# JDBC-MAVEN
导入
1. mysql驱动程序包
2. spring对jdbc的支持

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>${mysql.connector.version}</version>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>${spring.version}</version>
</dependency>
```

