---
title: 开启注解扫描
tags: 
- 开启注解扫描
categories: 
- Spring 
---

与XML方式功能一样,只是spring提供的另一种实现方式，更加适用于bean的实现类由用户自己开发的场景

# 开启注解扫描

想使用注解，第一步，开启注解扫描,开启可以使用xml方式，也可以使用注解方式

## 使用xml开启注解
```xml
<!-- base-package传入如，com.liisyu.spring.service,那么它就会扫描该类所在的包 -->
<context:component-scan base-package="" />
```

## 使用java开启注解
通过@ComponentScan来开启,一般配合@Configuration注解使用
```java
@Configuration
@ComponentScan(basePackages="com.liisyu.spring.service")
public class SpringConfiguration(){
    ...
}
```

## @Configuration 注解

从Spring3.0开始使用，用于定义配置类，可替换xml文件，效果相当于< beans >根标签

当配置类内部包含一个或多个@Bean注解的方法时，这些方法将会被AnnotationConfigApplicationContext或AnnotationConfigWebApplicationContext类进行扫描，并用于构建bean定义，初始化Spring容器。

```java
@Configuration
public class SpringConfiguration(){
    ...
}
```

## @ComponentScan

效果相当于< context:component-scan >标签,扫描@Component、@Controller、@Service、@Repository注解的类。

属性:
1. basePackages：用于指定要扫描的包
2. value：和basePackages作用一样

不同方式的@ComponentScan扫描路径

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
