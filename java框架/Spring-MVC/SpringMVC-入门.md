---
title: SpringMVC-入门
date: 2018-02-28 00:00:01
tgas: 
- Spring-入门
categories: 
- SpringMVC
---

# 框架结构

![mvc](![spring](https://raw.githubusercontent.com/FameLsy/Images/master/spring/mvc.png))

流程解释:
1. 用户发送请求至前端控制器DispatcherServlet
2. DispatcherServlet收到请求调用HandlerMapping处理器映射器。
3. 处理器映射器根据请求url找到具体的处理器，生成处理器对象及处理器拦截器(如果有则生成)一并返回给DispatcherServlet。
4. DispatcherServlet通过HandlerAdapter处理器适配器调用处理器
5. HandlerAdapter执行处理器(handler，也叫后端控制器)
6. Controller执行完成返回ModelAndView
7. HandlerAdapter将handler执行结果ModelAndView返回给DispatcherServlet
8. DispatcherServlet将ModelAndView传给ViewReslover视图解析器
9. ViewReslover解析后返回具体View对象
10. DispatcherServlet对View进行渲染视图（即将模型数据填充至视图中）。
11. DispatcherServlet响应用户


# MVC组件说明
DispatcherServlet(前端控制器)：  
1. 用户请求到达前端控制器
2. 整个流程控制的中心，由它调用其它组件处理用户的请求，降低了组件之间的耦合性。

HandlerMapping(处理器映射器):  
1. 负责根据用户请求找到Handler处理器
2. springmvc提供了不同的映射器实现不同的映射方式，例如：配置文件方式，实现接口方式，注解方式等

Handler(处理器):  
1. 后端控制器，在DispatcherServlet的控制下Handler对具体的用户请求进行处理。  
2. 由于Handler涉及到具体的用户业务请求，所以一般情况需要程序员根据业务需求开发Handler。

HandlAdapter(处理器适配器)  
1. 对处理器进行执行
2. 使用了适配器模式，通过扩展适配器可以对更多类型的处理器进行执行。

ViewResolver：视图解析器
1. 负责将处理结果生成View视图
2. View Resolver首先根据逻辑视图名解析成物理视图名即具体的页面地址，再生成View视图对象，最后对View进行渲染将处理结果通过页面展示给用户。 

View：视图
1. springmvc框架提供了很多的View视图类型的支持
2. 常见试图包括：jstlView、freemarkerView、pdfView等。我们最常用的视图就是jsp。



# 配置

1. DispatcherServlet组件配置
2. 映射器和适注解配器配置
3. 试图解析器的配置

## DispatcherServlet组件配置

方式为在web.xml中配置
1. 设置了初始化参数contextConfigLocation,按指定路径加载配置文件
2. 如果不指定初始化参数，它会默认加载/WEB-INF/${servlet-name}-servlet.xml(本例就是/WEB-INF/springmvc-servlet.xml)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://java.sun.com/xml/ns/javaee"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
         version="2.5">

        <!-- 配置前端控制器 -->
        <servlet>
            <servlet-name>springmvc</servlet-name>
            <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
            <init-param>
                <param-name>contextConfigLocation</param-name>
                <param-value>classpath:springmvc.xml</param-value>
            </init-param>
            <load-on-startup>2</load-on-startup>
        </servlet>

        <servlet-mapping>
            <servlet-name>springmvc</servlet-name>
            <url-pattern>/</url-pattern>
        </servlet-mapping>
    </web-app>
```

## 注解映射器和适注解配器配置

什么是注解映射器和适注解配器?

RequestMappingHandlerMapping(注解式处理器映射器):  
1. 对类中标记@ResquestMapping的方法进行映射
2. 根据ResquestMapping定义的url匹配ResquestMapping标记的方法，匹配成功返回HandlerMethod对象给前端控制器，HandlerMethod对象中封装url对应的方法Method。 

RequestMappingHandlerAdapter(注解式处理器适配器)
1. 对标记@ResquestMapping的方法进行适配

注解映射器和适配器配置方式
1. < bean >标签配置
2. 通过mvc标签配置（推荐）

### < bean >标签配置

配置
```xml
<!--注解映射器 -->
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping"/>
<!--注解适配器 -->
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter"/>
```

### 通过mvc标签配置（推荐）

功能更加强大,除了配置方式一的bean,还由其他bean.
```xml
<mvc:annotation-drivern />
```

## 视图解析器配置

视图解析器配置

```xml
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
            <!-- 该视图解析器，默认的视图类就是JstlView，可以不写 -->
		<property name="viewClass"
			value="org.springframework.web.servlet.view.JstlView" />
		<property name="prefix" value="/WEB-INF/jsp/" />
		<property name="suffix" value=".jsp" />
	</bean>
```

InternalResourceViewResolver:支持jsp解析  

viewClass：JstlView表示JSP模板页面需要使用JSTL标签库，所以classpath中必须包含jstl的相关jar 包。此属性可以不设置，默认为JstlView

refix 和suffix：查找视图页面的前缀和后缀，最终视图的址为：前缀+逻辑视图名+后缀，逻辑视图名需要在controller中返回的ModelAndView指定，比如逻辑视图名为hello，则最终返回的jsp视图地址 “WEB-INF/jsp/hello.jsp”