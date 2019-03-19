#  注册DispatcherServlet

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