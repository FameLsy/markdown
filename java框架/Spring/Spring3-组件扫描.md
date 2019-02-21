---
title: Spring(3)：组件扫描
date: 2018-02-03 00:00:03
tgas: 
- Spring 组件扫描
categories: 
- Spring 
---

# 组件扫描

组件扫描默认是不开启的,但是可以
1. 通过@ComponentScan可以启用组件扫描
2. 通过xml开启

## 通过xml开启

```xml
<context:component-scan base-package="xxx.xxx.xxx">
```

## 对于组建扫描的包设置

方式一：类所在的包就是基础包
```java
@ComponentScan
```

方式二：指定基础包（String 形式）
```java
@ComponentScan(“packageName”)
```

方式三：指定基础包更清晰版（String形式）
```java
@ComponentScan(basePackages = “packageName”)
```
方式四：指定多个基础包（String 形式）
```java
@ComponentScan(backPackages = {“packageName1”, ”packageName2”})
```
方式五：通过类来设置类的所在的包为基础包
```java
@ComponentScan(basePackageClasses={DemoClass.class, DemoClass2.class})
```
>为什么要特地表明2、3、4是string 形式：因为String是类型不安全的（not type-safe）,而5更加安全。5设置可以添加一个空标记接口，只用来设置基础包，这是有利于代码重构的

## 为组件扫描的bean命名

1. 默认
2. 自定义
### 默认命名方式

没有明确命名bean的id，则id为类名首字母小写。如CDPlayer的bean id就是cDplayer

### 自定义命名方式

第一种方式
```java
@Component("cDplayerName")
```

第二种方式

@Name注解来自Java依赖注入规范（Java Dependency Injection），与@Component只有细微差别，大多数情况下可以互换【个人觉得知道就好，没必要用】

```java
@Name("cDplayerName")
```
