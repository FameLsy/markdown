---
title: 视图解析器配置
tgas: 
- 视图解析器配置
categories: 
- SpringMVC
---

# 视图解析器配置

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