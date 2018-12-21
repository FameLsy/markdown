---
title: maven
tags: 
- maven
categories:
- maven
---

# 安装
[MAVEN下载地址](http://maven.apache.org/download.cgi)

maven下载有src(源码版本)和bin(二进制版本)，选择bin版本
>注意：  
>Maven依赖JDK

**解压**
```
sudo tar xzvf apache-maven-3.6.0-bin.tar.gz -C /opt
```

**配置**
```
 vim ~/.bashrc
 ```
添加
```
export M2_HOME=/opt/apache-maven-3.6.0
export PATH=${M2_HOME}/bin:$PATH
```

**测试**
```
mvn --version
```
# MAVEN 目录

|目录名|说明|
|--|--|
|bin|mvn相关脚本|
|boot|只包含plexus-classworlds文件,类加载器框架|
|conf|配置文件|
|lib|JAVA类库文件|
|LICENSE|*|
|NOTICE|*|
|README.txt|*|


# ~/.m2 目录
所有Maven构件都被存储到该仓库中。

# 配置
**全局范围**
```
M2_HOME/conf/settings.xml
```
**用户范围**
```
~/.m2/settings.xml
```
# 项目目录结构

|目录|说明|
|--|--|
|pom.xml|包含此项目的项目对象模型|
|$ {basedir} / src / main / java|应用程序源|
|$ {basedir} / src / test / java|测试源|
|$ {basedir} / target / classes|编译后的文件|
# 编译应用程序源
```
mvn compile
```
>注意：  
>编译时可能失败，错误信息如下  
>[ERROR] Source option 5 is no longer supported. Use 6 or later.  
>[ERROR] Target option 1.5 is no longer supported. Use 1.6 or later.  
>原因是maven默认的maven-compiler-plugin编译器版本过低

**修改编译器版本**
```xml
<project
 ……
<build>
  <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <!-- since 2.0 -->
        <version>3.7.0</version>
        <configuration>
            <!-- use the Java 8 language features -->
            <source>1.8</source>
            <!-- want the compiled classes to be compatible with JVM 1.8 -->
            <target>1.8</target>
            <!-- The -encoding argument for the Java compiler. -->
            <encoding>UTF8</encoding>
        </configuration>
    </plugin>
  </plugins>
</build>

</project>
```
或者
```xml
<project
……
  <properties>
    <maven.compiler.source>1.7</maven.compiler.source>
    <maven.compiler.target>1.7</maven.compiler.target>
  </properties>
</project>
```



# pom.xml
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">

  <modelVersion>4.0.0</modelVersion>
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <packaging>jar</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>my-app</name>
  <url>http://maven.apache.org</url>
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
```

|元素|说明|
|--|--|
|project| pom.xml的顶级元素|
|modelVersion|POM使用的对象模型的版本,很少变化|
|groupId|创建项目的组织或组|
|artifactId |项目生成的artifact(通常是JAR文件)的唯一名称|
|packaging|指示aritfact要使用的包类型,默认jar|
|version|aritfact的版本，SNAPSHOT表示开发状态|
|name|项目显示名称，通常用于Maven生成的文档中|
|url|指示可以找到项目站点的位置。这通常用于Maven生成的文档中|
|description|项目的基本描述。这通常用于Maven生成的文档中|

# 原型(Archetypes)
**什么是原型**  
Archetype是一个Maven项目模板工具包  
**使用原型**
```
mvn archetype ：generate
```
