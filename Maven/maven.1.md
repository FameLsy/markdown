---
title: maven(1) 基础介绍
tags: 
- maven 基础介绍
categories:
- maven
---


Maven是纯java开发

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
# 或
mvn-v
```

# MAVEN 目录
maven 有两个目录
1. 安装目录，包含maven各种组件等
2. .m2目录，存在于用户目录下

## 安装目录
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


# 项目目录结构

一般的maven项目目录结构

|目录|说明|
|--|--|
|pom.xml|包含此项目的项目对象模型|
|$ {basedir} / src / main / java|应用程序源|
|$ {basedir} / src / test / java|测试源|
|$ {basedir} / target / classes|编译后的文件|

# 配置
maven的配置文件存在于两个地方
1. 全局，安装目录下
2. 用户范围，用户目录下

## 全局范围
```
M2_HOME/conf/settings.xml
```
## 用户范围
```
~/.m2/settings.xml
```