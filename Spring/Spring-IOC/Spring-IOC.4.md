---
title: spring-IOC-Xml的使用
tags: 
- 基于XML的使用
categories: 
- Spring 
---

在Spring 的XML配置文件中配置一个bean标签，该标签最终会被加载为一个BeanDefition对象（描述对象信息）

XML方式的实现，适合于来自第三方，使用其它框架时使用


配置文件头:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

</beans>
```