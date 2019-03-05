---
title: Servlet-配置
- Servlet-配置
categories: 
- Servlet
---

# Servlet-配置

主要配置
1. < servlet >:配置类
2. < servlet-mapping >：配置访问方式

## 配置serlvet
定义完Servlet类后，还需要告诉Web容器该如何访问这个Servlet，而这个配置在web.xml中完成
1. < servlet-name >: 可取任意字符串，唯一,用于其他标签使用(如< servlet-mapping >)
2. < servlet-class>: 配置Serlvet类
3. < init-param >: (可选)用于配置初始化参数  
    < param-name >: 配置参数名
    < param-value >: 配置参数值
4. < load-on-startup >: 配置加载方式  
    值大于0: 在启动时加载该Servlet，值越小，优先级越高
    值为0：在第一次请求时加载

```xml
    <servlet>
        <servlet-name>MyServlet</servlet-name>
        <servlet-class>com.liisyu.servlet</servlet-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
```

## 配置servlet访问方式

配置好servlet类后，需要配置对其的访问方式，使用< serlvet-mapping >标签
1. < servlet-name>: 指定引用的Serlvet名称，Servlet会使用该访问方式
2. < url-pattern >: 配置该serlvet的访问方式  
    如：/servlet,想要访问MyServlet，那么url为:http://localhost:8080/ + 项目路径(如/serlvet) + ${url-pattern}  
    允许通配符 * ,代表任意字符,如 /servlet/FirstServlet.*,表示可以访问任意后缀的FirstServlet  

```xml
    <servlet-mapping>
        <servlet-name>MyServlet</servlet-name>
        <url-pattern>/servlet/MyServlet.*</url-pattern>
    </servlet-mapping>

```


获取配置的初始化参数值
```java
// servlet方法
getServletContext().getInitParam(String paramName)
```