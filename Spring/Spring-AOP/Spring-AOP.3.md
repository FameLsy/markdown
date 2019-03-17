---
title: Spring-AOP-切入点表达式
tgas: 
- Spring-AOP-切入点表达式
categories: 
- Spring 
---

# 切入点表达式
格式：
```xml
execution([修饰符] 返回类型 包名.类名.方法名(参数)
```
1. execution:必须
2. 修饰符：可省略
3. 返回类型：必须，可用*代替
4. 包名：  
    多级包之间用 . 分割  
    包名可用*代替，多级包使用多个*代替  
    生路中间包名可以用..(如*..*.UserDaoImpl.insert())
5. 类名:  
    可用*代替  
    也可以用*通配(如,*DaoImpl)  
6. 方法名：  
    可以使用*代替  
    可以使用*通配(如,add*)  
7. 参数：  
    可以使用*代替  
    多个参数，可以使用..代替  