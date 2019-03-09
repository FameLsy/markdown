---
title: PropertySource注解
tags: 
- PropertySource注解
categories: 
- Spring 
---

# @PropertySource

效果相当于< context:property-placeholder >标签，加载properties配置文件

属性：
1. value[]: 用于指定properties文件路径，如果在类路径下，需要写上classpath

```java
@Configuration
@PropertySource(“classpath:jdbc.properties”)
public class JdbcConfig {
    @Value("${jdbc.driver}")
    private String driver;
    @Value("${jdbc.url}")
    private String url;
```

