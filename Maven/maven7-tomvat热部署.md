---
title: maven(7) tomcat热部署
date: 2019-02-21 00:00:07
tags: 
- maven tomcat热部署
categories:
- maven
---

# 开启tomcat 热部署

热部署：开启状态下部署war包

1. 修改tomcat/conf/tomcat-users.xml 配置文件；
2. 添加
修改< tomcat-users>标签
```xml
<role rolename="manager-gui"/>
<role rolename="manager-script"/>
<user username="liisyu" password="123456" roles="manager-gui, manager-script"/>
```

3. 启动tomcat；

4. 修改项目中pom.xml，在tomcat7的插件上添加以下代码

```xml
	<plugin>
  			<groupId>org.apache.tomcat.maven</groupId>
  			<artifactId>tomcat7-maven-plugin</artifactId>
  			<version>${tomcat7-maven-plugin.version}</version>
  			<configuration>
  				<url>http://localhost:8080/xxxx</url>
  				<username>liisyu</username>
  				<password>123456</password>
  			</configuration>
  		</plugin>
```

5. 运行命令
```
//第一次部署
tomcat:deploy
//再一次
tomcat:redeploy
```