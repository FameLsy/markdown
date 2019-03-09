---
title: 注解式处理器适配器组件配置
tgas: 
- 注解式处理器适配器组件配置
categories: 
- SpringMVC
---

# 注解式处理器适配器配置

RequestMappingHandlerAdapter(注解式处理器适配器)
1. 对标记@ResquestMapping的方法进行适配

配置
```xml
<!--注解适配器 -->
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter"/>
```

另一种配置功能更加强大,除了配置了方式一的RequestMappingHandlerAdapter bean,还由其他bean（如注解式处理器映射器）.
```xml
<mvc:annotation-drivern />
``````