---
title: Spring-IOC-profile
tags: 
- Spring-IOC-profile
categories: 
- Spring 
---

作用：  
通过激活不同的profile，达到加载不同的bean,可以使用注解，也可以使用xml配置

# @Profile/ActiveProfiles

基于类级别的profile
```java
@Profile("dev")
public class ClassprofilesConfig {
    @Bean
    public CompactDisc compactDisc(){
        return new SgtPeppers();
    }
```
基于方法级别的profile
```java
@Bean 
@Profile("dev")
public CompactDisc compactDisc(){
    return new SgtPeppers();
}
```

利用@ActiveProfiles注解来激活某个profile
```
@ActiveProfiles("dev")
```

>注意：@profile本质上是使用了@Conditional来创建的注解


# xml中定义profile

只有激活profile才会使用到这个配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd"
       profile="dev">
```

可以使用嵌套beans的方式，而不是为为每一个环境设置一个profile

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd"
       profile="dev">

       <beans profile="xxx"></beans>
       <beans profile ="sdsd"></beans>
```
Spring 激活profile主要依赖两个属性
1. spring.profiles.active
2. spring.profiles.default


有多种方式来设置这两个属性
1. 作为DispatcherServlet的初始化参数；
2. 作为Web应用的上下文参数；
3. 作为JNDI条目；
4. 作为环境变量；
5. 作为JVM的系统属性；
6. 在集成测试类上，使用@ActiveProfiles注解设置。

第一种方式示例
```xml
<!-- web.xml -->
<!-- 为上下文设置默认的profile -->
    <context-param>
        <param-name>spring.profiles.default</param-name>
        <param-value>/WEN-INF/spring/root-context.xml</param-value>
    </context-param>

    <servlet>
        ...
        <init-param>
            <param-name>spring.profiles.default</param-name>
            <param-value>dev</param-value>
        </init-param>
    </servlet>
```


