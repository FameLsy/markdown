---
title: SpringMVC-Controller返回值
date: 2018-02-28 00:00:01
tgas: 
- SpringMVC-Controller返回值
categories: 
- SpringMVC
---
主要分为
1. 不适用注解方式
2. 使用注解方式

# Controller返回值(不使用注解)

Controller返回值可以是
1. ModelAndView类
2. void
3. 返回字符串(推荐)  

## 返回ModelAndView

controller方法中定义ModelAndView对象并返回，对象中可添加model数据、指定view


设置数据模型,相当于request的setAttribute方法，
先将k/v数据放入map中，最终根据视图对象不同，再进行后续处理

```java
ModelAndView mvAndView = new ModelAndView();

mvAndView.addObject("itemList", itemList);

// 设置视图(逻辑路径)
mvAndView.setViewName("item/item-list");

```

## void
在controller方法形参上可以定义request和response

方法写法如下
```java
void doSomething(HttpServletRequest request,HttpServletResponse response){
    ...
}
```

通过request和response,进行Servlet操作

## 返回字符串

返回字符串由三种表示方式

第一种： 返回逻辑视图名
```java
return "item/item-list"
```
想要传数据，则只需要传入Model参数，并对其进行操作
```java
void doSomething(HttpServletRequest request,Model model){
    model.addAttribute(key, value);
    return "item/item-list";
}
```

第二种：redirect重定向
```java
return "redirect:testRedirect"
```

相当于“response.sendRedirect()”,且浏览器URL发生改变，Request域不能共享

第三种： forward转发

```java
return "forward:testForward"
```

相当于“request.getRequestDispatcher().forward(request,response)”,浏览器URL不发送改变,Request域可以共享


# Controller返回值(使用注解)

关于Controller返回值的注解，只要有
1. @ResponseBody
2. @RequestBody

## @ResponseBody
```java
@ResponseBody
public Demo 
```

1. 该注解可以通过内置的9种HttpMessageConverter，匹配不同的Controller返回值类型，然后进行不同的消息转换处理。
2. 将转换之后的数据放到HttpServletResponse对象的响应体返回到页面
3. 不同的HttpMessageConverter处理的数据，指定的ContentType值也不同。

## @RequestBody

该注解的作用和@ResponseBody注解正好相反，它是处理请求参数的Http消息转换的。

## 常用的HttpMessageConverter

MappingJacksonHttpMessageConverter：
1. 专门处理POJO类型
2. 默认使用MappingJackson的JSON处理能力，将后台返回的Java对象（POJO类型），转为JSON格式输出到页面
3. 将响应体的Content-Type设置为application/json；charset=utf-8

StringHttpMessageConverter:
1. 专门处理String类型
2. 调用response.getWriter()方法将String类型的字符串写回给调用者
3. 将响应体的Content-Type设置为text/plain；charset=utf-8