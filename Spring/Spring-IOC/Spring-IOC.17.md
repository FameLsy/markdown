---
title: 混合导入配置
tags: 
- 混合导入配置
categories: 
- Spring 
---

混合导入一共有种方式
1. 配置类中导入其他配置类
2. 配置类中导入xml配置文件
3. xml配置文件导入配置类
4. xml配置文件导入其他xml配置文件

# 配置类种导入其他配置类

通过@Import导入

```java
@Configuration
@Import(CDConfig.class)
public class CDPlayerConfig {
```


# 配置类中导入xml配置文件

通过@ImportResource

```
@Configuration
@ImportResource("classpath:xxx/xxx/cd-config.xml")
public class CDPlayerConfig {
```

# xml配置文件导入配置类

直接使用< bean >标签即可

```
    <bean class="com.spring.action.mixedconfig.CDConfig"/>
```

# xml配置文件导入其他xml配置文件
使用< import >标签导入即可

```xml
    <import resource="cd-config.xml"/>
```
