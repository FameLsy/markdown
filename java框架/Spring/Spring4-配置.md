---
title: Spring(4)：配置
date: 2018-02-03 00:00:04
tgas: 
- Spring 配置
categories: 
- Spring 
---

# 导入和混合配置

## 方式一：javaConfig中导入其他的JavaConfig
通过@Import导入

```java
@Configuration
@Import(CDConfig.class)
public class CDPlayerConfig {
```

## 方式二：JavaConfig中导入XML

通过@ImportResource
```
@Configuration
@ImportResource("classpath:xxx/xxx/cd-config.xml")
public class CDPlayerConfig {
```

## 方式三：在XML中引入JavaConfig
直接使用< bean >标签

```
    <bean class="com.spring.action.mixedconfig.CDConfig"/>
```

## 方式四：在XML中导入XML

使用< import >标签导入即可

```xml
    <import resource="cd-config.xml"/>
```

## 技巧

甚至一个顶级的配置文件，可以是javaconfig,也可以是xml,将所有的配置全集中在一起，且开启扫描也再次完成。  

导入的配置类可以省略@Configuration注解

# 配置文件的加载

## 方式一：自动加载当前类名+context.xml后缀的配置文件。如CDPlayerContext.xml
```java
@ContextConfiguration
```
## 方式二：加载配置类
```java
@ContextConfiguration(classes = JavaConfig.class)
```
## 方式三:加载多个配置类
```java
@ContextConfiguration(classes = {JavaConfig.class, JavaConfig2.class})
```
## 方式四：加载指定的xml文件
```java
@ContextConfiguration(locations=”classpath:xxx/xxx.xml”)
```
## 方式五：加载多个指定的xml文件
```java
@ContextConfiguration(locations={“classpath:xxx/xxx.xml” , “classpath:xxx/xxx2.xml”})
```
>注：用idea开发需要把xml文件放到resources中，idea不会读src下的静态资源文件~~~