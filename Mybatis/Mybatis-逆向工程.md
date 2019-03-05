---
title: Mybatis-逆向工具
date: 2019-02-16 00:00:01
tags: 
- Mybatis-逆向工具
categories: 
- Mybaits
---

MyBaits Generator是由Mybatis官方提供的一种逆向代码生成器，会根据数据库表生成相关代码
1. POJO
2. Mapper接口
3. SQL Mapper XML

MyBatis提供了多种实现方式
1. 命令行
2. Maven Plugin
3. Java 程序

# Maven Plugin方式

依赖:
1. mybatis依赖
2. mybatis-generator-maven插件
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.liisyu</groupId>
    <artifactId>mybaits-generator</artifactId>
    <version>1.0-SNAPSHOT</version>
    <properties>
        <maven.compiler.plugin.version>3.8.0</maven.compiler.plugin.version>
        <mysql.connector.version>5.1.6</mysql.connector.version>
        <mybatis.generator.version>1.3.7</mybatis.generator.version>
        <mybatis.version>3.4.6</mybatis.version>
    </properties>
    <dependencies>
        <!--mybatis依赖-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>${mybatis.version}</version>
        </dependency>
    </dependencies>
    <build>
        <plugins>
            <!--maven的JDK编译级别-->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>${maven.compiler.plugin.version}</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <!--mybatis-generator-maven插件-->
            <plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>${mybatis.generator.version}</version>
                <dependencies>
                    <!--如果出现找不到jdbc.driver,需要添加该依赖-->
                    <dependency>
                        <groupId>mysql</groupId>
                        <artifactId>mysql-connector-java</artifactId>
                        <version>${mysql.connector.version}</version>
                    </dependency>
                </dependencies>
            </plugin>
        </plugins>

    </build>

</project>
```

配置文件: mybaits generator 会自动寻找目录下的resources/generatorConfig.xml

mybatis配置文件提供的标签
1. commentGenerator: 用于生成的各种元素生成注释
2. jdbcConnection： 用于连接数据库
3. javaTypeResolver：用于定义Java类型解析器的属性
4. javaModelGenerator：定义Java模型生成器的属性
5. sqlMapGenerator：用于定义SQL映射生成器的属性
6. javaClientGenerator：用于定义Java客户端生成器的属性

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <context id="testTables" targetRuntime="MyBatis3">
        <commentGenerator>
            <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
            <property name="suppressAllComments" value="true" />
        </commentGenerator>

        <!--数据库连接的信息：驱动类、连接地址、用户名、密码 -->
        <jdbcConnection driverClass="com.mysql.jdbc.Driver"
                        connectionURL="jdbc:mysql://localhost:3306/mydb?serverTimezone=GMT%2B8" userId="masorl"
                        password="314512321lsy">
        </jdbcConnection>


        <!-- 默认false，把JDBC DECIMAL 和 NUMERIC 类型解析为 Integer，为 true时把JDBC DECIMAL
            和 NUMERIC 类型解析为java.math.BigDecimal -->
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false" />
        </javaTypeResolver>

        <!-- targetProject:生成PO类的位置 -->
        <javaModelGenerator targetPackage="com.liisyu.po"
                            targetProject=".\src\main\java">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false" />
            <!-- 从数据库返回的值被清理前后的空格 -->
            <property name="trimStrings" value="true" />
        </javaModelGenerator>
        <!-- targetProject:mapper映射文件生成的位置 -->
        <sqlMapGenerator targetPackage="com.liisyu.mapper"
                         targetProject=".\src\main\java">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false" />
        </sqlMapGenerator>
        <!-- targetPackage：mapper接口生成的位置 -->
        <javaClientGenerator type="XMLMAPPER"
                             targetPackage="com.liisyu.mapper"
                             targetProject=".\src\main\java">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false" />
        </javaClientGenerator>

        <!-- 指定数据库表 -->
        <!-- domainObjectRenamingRule通过正则表达式，去除签所有*_开头的前缀 -->
        <table tableName="t_account">
            <domainObjectRenamingRule searchString="^[^_]" replaceString=""/>
        </table>

    </context>
</generatorConfiguration>
```

通过maven插件方式运行生成
```
mybatis-generator:generate
```