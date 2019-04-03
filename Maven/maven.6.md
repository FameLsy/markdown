---
title: maven(6) 依赖
tags: 
- maven 依赖
categories:
- maven
---

# Scope依赖作用域

1. compile（默认值）：在编译、运行、测试、打包都有效；
2. provided：编译、测试时有效，运行、打包无效；
3. test：仅在测试时有效；
4. runtime：测试、运行、打包时有效；
5. system：不推荐使用，使用system作用域不会去本地仓库寻找依赖，要指定本地路径；

![依赖关系](https://raw.githubusercontent.com/FameLsy/Images/master/maven/%E4%BE%9D%E8%B5%96%E5%85%B3%E7%B3%BB%E4%B8%AD%E4%BD%9C%E7%94%A8%E5%9F%9F%E8%AF%B4%E6%98%8E.png)


# 父/子项目

父：
```
<modules>
    <module>child artifactid</module>
</modules>
```

子:
```
<parent>
    <groupId></groupId>
    <artifactId></artifactIf>
    <version></version>
</parent>
```

# 依赖冲突

依赖传递：添加依赖的jar包如果还依赖其他jar包，则在添加时会一起引入；


如下图所示:
1. Base-project由3个子项目组成，A-project依赖Tools.jar 1.0版本，C-project依赖Tools.jar 2.0版本
2. 根据依赖传递机制，Base-project将同时依赖Tools.jar两个版本，但这造成了冲突。根据路径最近获胜策略，该项目最终依赖Tools.jar 1.0，导致C-project无法调用getAll方法从而报错


![依赖冲突](https://raw.githubusercontent.com/FameLsy/Images/master/maven/%E4%BE%9D%E8%B5%96%E5%86%B2%E7%AA%81.png)

解决:
1. 直接添加Tools.jar 2.0依赖；
2. 排除：使用< dependency>标签中的< exclusions>标签排除A-project中的Tools.jar 1.0依赖；(依赖A标签但排除tools.jar.1.0)

```xml
<dependency>
    ...
    <exclusions>
        <exclusion>
            <groupId></groupId>
            <artifactId></artigactId>
        </exclusion>
    </exclusions>
```
3. 使用依赖管理，版本锁定(锁定后其他地方无需再写版本号，因为已经锁定了)
```
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId></groupId>
            <artifactId></artgactId>
            <version></version>
        </dependency>
    </dependencies>
</dependencyManagement>
```

## 过滤资源
maven 只会把.java文件编译后放入target/classes文件夹，而忽略其他文件。  
所以，静态资源文件需要放行

如下
```
<project>
...
<build>
    <resources>
      <resource>
        <directory>src/main/resources</directory>
        <includes>
        <!-- **表示所有层级 -->
            <include>**/*.properties</include>
            <include>**/*.xml</include>
        </includes>
        <filtering>false</filtering>
      </resource>
    </resources>
  </build>
...
</project>
```