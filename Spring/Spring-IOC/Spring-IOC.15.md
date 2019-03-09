---
title: Primary注解
tags: 
- Primary注解
categories: 
- Spring 
---


# @Primary

效果相当于
```xml
    <bean id="" class="" primary="true"/>
```
标示首选的bean处理歧义

使用 @Primary标签
```java
@Component
@Primary
public class xxx
//或者
@Bean
@Primary
public XXX getXXX(){}
```



