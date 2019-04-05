<!-- ---
title: maven(5) 常用命令
tags: 
- maven 仓库
categories:
- maven
--- -->

# 创建文档
```
mvn archetype:generate \
  -DarchetypeGroupId=org.apache.maven.archetypes \
  -DarchetypeArtifactId=maven-archetype-site \
  -DgroupId=com.mycompany.app \
  -DartifactId=my-app-site
```
# 构建其他项目类型
```
mvn archetype:generate \
    -DarchetypeGroupId=org.apache.maven.archetypes \
    -DarchetypeArtifactId=maven-archetype-webapp \
    -DgroupId=com.mycompany.app \
    -DartifactId=my-webapp
```

# 其他Maven常用命令：

clean：		清理，编译后的目录；  

compile：	编译，只编译main目录，不编译test中的代码；  

test-compile：编译test目录中的代码；  

test：		运行test里边的代码；  

package：	打包  
1. java项目->打成jar包；
2. web项目->打成war包；

install：发布项目到本地仓库，用在打jar包上，打成jar包可以被其他项目使用；  

tomcat:run：一键构建项目；  
