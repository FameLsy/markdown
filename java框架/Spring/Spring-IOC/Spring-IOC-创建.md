---
title: Spring-IOC-创建
tags: 
- Spring-IOC-创建
categories: 
- Spring 
---

关于创建IOC容器，主要分两种方式
1. 在java应用中创建
2. 在java web应用中创建

# 在java应用中创建

## 通过配置文件创建

实例化ClassPathXmlApplicationContext对象即可
```java
ApplicationContext context = new ClassPathXmlApplicationContext(xmlpath);
```

## 通过配置文件创建

使用AnnotationConfigApplicationContext类获取容器
```java
ApplicationContext context = 
new AnnotationConfigApplicationContext(SpringConfiguration.class);

UserService service = context.getBean(UserService.class);
service.saveUser();
```



# 在java web应用中创建

需要在web.xml中配置ContextLoaderListener监听器，并配置ContextConfigLocation参数,传入配置类或配置文件

执行流程如下:
1. web容器启动之后加载web.xml，此时加载ContextLoaderListener监听器(该监听器实现了ServletContextListener接口)
2. 监听器会在web容器启动的时候，触发contextInitialized()方法
3. contextInitialized()方法会调用initWebApplicationContext()方法，该方法负责创建Spring容器（DefaultListableBeanFactory）

## 通过配置文件创建

```xml
<!-- 通过配置类获取IOC容器 -->
<web-app>
    <context-param>
        <param-name>contextClass</param-name>
        <param-value>
            org.springframework.web.context.
            support.ClassPathXmlApplicationContext
        </param-value>
    </context-param>
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>com/liisyu/main/resource/applicationContext.xml</param-value>
    </context-param>
    <listener>
        <listener-class>
            org.springframework.web.context.ContextLoaderListener
        </listener-class>
    </listener>
</web-app>
```

## 通过配置类创建
```xml
<!-- 通过配置类获取IOC容器 -->
<web-app>
    <context-param>
        <param-name>contextClass</param-name>
        <param-value>
            org.springframework.web.context.
            support.AnnotationConfigWebApplicationContext
        </param-value>
    </context-param>
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>
            com.liisyu.spring.test.SpringConfiguration
        </param-value>
    </context-param>
    <listener>
        <listener-class>
            org.springframework.web.context.ContextLoaderListener
        </listener-class>
    </listener>
</web-app>
```

关于监听器，可参考Servlet章节:  
[servlet-监听器-分类]()
