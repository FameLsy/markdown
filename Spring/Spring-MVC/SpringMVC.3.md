#  注册HandlerMapping


RequestMappingHandlerMapping(注解式处理器映射器):  
1. 对类中标记@ResquestMapping的方法进行映射
2. 根据ResquestMapping定义的url匹配ResquestMapping标记的方法，匹配成功返回HandlerMethod对象给前端控制器，HandlerMethod对象中封装url对应的方法Method

配置
```xml
<!--注解映射器 -->
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping"/>

另一种配置功能更加强大,除了配置了方式一的RequestMappingHandlerMapping bean,还由其他bean(如注解式处理器适配器).
```xml
<mvc:annotation-drivern />
``````