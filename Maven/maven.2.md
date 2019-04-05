<!-- ---
title: maven(2) 仓库
tags: 
- maven 仓库
categories:
- maven
--- -->


# Maven的三种仓库

## 仓库的查找顺序

![仓库的查找顺序](https://raw.githubusercontent.com/FameLsy/Images/master/maven/maven%E4%BB%93%E5%BA%93%E6%9F%A5%E6%89%BE%E9%A1%BA%E5%BA%8F.png)

## 坐标

坐标：
1. 由groupId + artifactId + version组成
2. maven项目通过坐标方式从本地仓库获取jar包

## 本地仓库

本地仓库jar包从中央仓库中下载（除了自己开发的jar包）  

自定义设置本地仓库：%maven%/conf/setting.xml
```
# 默认位置
${user.home}/.m2/repository
# 自定义位置修改
 <localRepository>/path/to/local/repo</localRepository>
```

## 远程仓库

## 中央仓库

[中央仓库地址](https://mvnrepository.com/)

## 配置阿里镜像

在setting.xml中配置
```
<!-- 找到mirrors标签,在里面添加添加 -->
<mirror>
    <id>alimaven</id>
    <name>aliyun maven</name>
    <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
    <mirrorOf>central</mirrorOf>
</mirror>
```

