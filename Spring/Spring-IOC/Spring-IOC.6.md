---
title: bean的三种实现方式
tags: 
- bean的三种实现方式
categories: 
- Spring 
---


Spring bean进行实例化的，总共有三种
1. 使用默认无参构造函数(重点)
2. 静态工厂(了解)
3. 实例工厂(了解)

# 使用默认无参构造函数

使用方式
```java
<bean id="" class=""/>
```

默认情况，如果bean对应的class没有无参构造函数，就会报错

>注  
>如果不指定id,那么bean的 id将会是类的全名
> 如class="com.liisyu.dao.impl.UserDaoImpl"
>那么bean的默认id="com.liisyu.dao.impl.UserDaoImpl"，而不是userDaoImpl

# 静态工厂

步骤:
1. 自定义一个静态工厂
2. 通过factory-method属性指向静态工厂

自定义一个静态工厂
```java
public class StaticFactory {
    public static UserService createUserService(){
        return new UserServiceImpl();
    }
}
```

通过factory-method属性指定生产对象的静态方法
```xml
<bean id="" class="com.kkb.spring.factory.StaticFactory" factory-method="createUserService"></bean>
```

# 实例工厂

步骤:
1. 自定义一个实例工厂
2. 为实例工厂创建bean,包含一个获取实例的方法
3. 再调用实力工厂的创建方法

自定义一个实例工厂
```java
public class InstanceFactory {
    public UserService createUserService(){
        return new UserServiceImpl();
    }
}
```

先为实例工厂创建bean
```xml
<bean id="instancFactory" class="com.kkb.factory.InstanceFactory"></bean>
```

再调用实例工厂的获取方法
```xml
<bean id="" factory-bean="instancFactory" factory-method="createUserService"></bean>
```
