---
title: SpringMVC-@RequestMapping
tgas: 
- SpringMVC-@RequestMapping
categories: 
- SpringMVC
---

在Spring-MVC通过@RequestMapping注解，可以定义不同的处理器映射规则

# URL路径映射
将@RequstMapping配置到方法上，进行URL路径的映射

```java
@RequestMapping(value="/item")或@RequestMapping("/item")
//多个url 映射到同一方法上去
@RequestMapping(value={"/item",”/queryItem”})
```

# 窄化请求映射
将@RequstMapping配置到class类上，指定通用的请求前缀， 限制此类下的所有方法的访问请求url必须以请求前缀开头，通过此方法对url进行模块化分类管理。

如,想要访问add方法，那请求url必须是/item/add
```java
@RequestMapping(value="/item")
class demo{
    @RequestMapping("/add")
    public void add(){
        ...
    }
}
```
# 请求方法限定 

主要是限制客户端访问的请求方式
1. 限定GET请求，,@RequestMapping(method = RequestMethod.GET)
2. 限定POST请求，@RequestMapping(method = RequestMethod.POST)
3. GET，POST都可以,@RequestMapping(method={RequestMethod.GET,RequestMethod.POST})