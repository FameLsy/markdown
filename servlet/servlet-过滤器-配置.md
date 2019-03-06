---
title: Servlet-过滤器-配置
tags: 
- Servlet-过滤器-配置
categories: 
- Servlet
---

# Filter配置

Filter需要配置在web.xml中才能生效,用法类似于Serlvet的配置

```xml
<!-- web.xml -->
    <filter>
        <filter-name>MyFilter</filter-name>
        <filter-class>com.liisyu.MyFilter</filter-class>
        <init-param>
            <param-name>param</param-name>
            <param-value>param</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>MyFilter</filter-name>
        <url-pattern>/jsp/*</url-pattern>
        <url-pattern>*.do</url-pattern>
        <dispatcher>REQUEST</dispatcher>
        <dispatcher>FORWARD</dispatcher>
    </filter-mapping>
```

在< url-pattern >标签中配置过滤规则，可以多个

在< dispatcher >标签中配置达到Servlet的方式
1. REQUEST: 表示仅当直接请求Serlvet时才生效,默认
2. FORWORD: 表示仅当某Servlet通过FORWORD到该Serlvet时才生效
3. INCLUDE: JSP相关
4. ERROR: JSP相关

>注:  
> 必须同时满足< url-pattern >和< dispatcher >,才能使Filter生效
> 多个Filter同时满足时，执行顺序按照< filter-mapping >的配置顺序执行