---
title: IOC注解
tags: 
- IOC注解
categories: 
- Spring 
---

# IOC注解

效果相当于< bean >标签，主要由
1. @Component:
2. @Controller:一般用于表现层的注解
3. @Service:一般用于业务层的注解
4. @Repository:一般用于持久层的注解
5. @Bean:通过标注在返回实例的方法上，将返回的实例注册为bean(主要用于注册第三方的bean)

@Component:  
1. 通过value属性 指定 bean 的 id(如果不指定 value 属性，默认 bean 的 id 是当前类的类名。首字母小写)

@Controller、@Service、@Repository注解:属于@Component:的语义化注解，功能一样

@Bean属性:  
1. name:给当前注解方法创建的对象指定一个名称(即 bean 的 id）
2. 不指定name,默认与标注的方法名相同
3. 注解默认作用域为单例singleton作用域，可通过@Scope()设置域；

```java
@Bean
@Scope("prototype")
public UserService userService(){
    return new UserServiceImpl();
}
```
