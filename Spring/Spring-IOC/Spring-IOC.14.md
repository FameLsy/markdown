---
title: 生命周期注解
tags: 
- 生命周期注解
categories: 
- Spring 
---

# 生命周期相关

有两个注解
1. @PostConstruct
2. @PreDestroy

效果相当于
```xml
<bean id="" class="" init-method="" destroy-method="" />
```

使用如下
```
@PostConstruct
public void init(){
    ...
}
@PreDestroy
public void  dostory(){
    ...
}
```

流程图如下

![生命周期](https://raw.githubusercontent.com/FameLsy/Images/master/spring/生命周期.png)

