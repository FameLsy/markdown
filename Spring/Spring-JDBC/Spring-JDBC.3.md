---
title: Spring管理第三方连接池
tgas: 
- Spring管理第三方连接池
categories: 
- Spring 
---



# Spring管理第三方连接池

## 管理DBCP

pom:
```xml
<!--dbcp连接池依赖-->
<dependency>
    <groupId>commons-dbcp</groupId>
    <artifactId>commons-dbcp</artifactId>
    <version>${dbcp.version}</version>
</dependency>
```

配置
```xml
<!--设置DBCP连接池-->
<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource">
    <property name="driverClassName" value="${db.driverClassName}"/>
    <property name="url" value="${db.url}"/>
    <property name="username" value="${db.username}"/>
    <property name="password" value="${db.password}"/>
</bean>
```

## 管理C3P0连接池

pom
```xml
<dependency>
    <groupId>com.mchange</groupId>
    <artifactId>c3p0</artifactId>
    <version>${c3p0.version}</version>
</dependency>
```

配置
```xml
<!--设置C3P0连接池-->
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="driverClass" value="${db.driverClassName}"/>
    <property name="jdbcUrl" value="${db.url}"/>
    <property name="user" value="${db.username}"/>
    <property name="password" value="${db.password}"/>
</bean>
```
