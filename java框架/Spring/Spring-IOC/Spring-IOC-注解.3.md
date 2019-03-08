---
title: DI注解
tags: 
- DI注解
categories: 
- Spring 
---

# DI注解

效果相当于< property >标签,主要有
1. @Autowired
2. @Qualifier
3. @Resource
4. @Value

## @Autowired:
1. 默认按类型装配（byType）
2. 这个注解是spring自身的
3. 默认情况下必须要求依赖对象必须存在,如果要允许null值，可以设置@Autowired(required=false)
4. 可以结合@Qualifier注解进行名称装配

## @Qualifier:
1. 不能独立使用，必须和@Autowire 一起使用(但是给方法参数注入时，可以独立使用。)
2. 在自动按照类型注入的基础之上，再按照 Bean 的 id 注入

可以通过@Qualifier和@Compontent联合使用，达到自定义bean的名称
```java
@Component
@Qualifier("bean_id")
...
```
当然，也可以通过@Qualifier和@Bean协同使用
```java
@Bean
@Qualifier("bean_id")
...
```

当需要为一个bean定义多个限定符时，又不能使用多个@Qulifier(同名注解只能存在一个)，所以自定义一个注解并使其拥有@Qulifier功能，完美解决这个问题

自定义方式如下
```java
package com.spring.ambiguity.quaifier;

import org.springframework.beans.factory.annotation.Qualifier;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target({ElementType.CONSTRUCTOR, ElementType.FIELD, ElementType.METHOD, ElementType.TYPE })
@Retention(RetentionPolicy.RUNTIME)
@Qualifier
public @interface Okss {
}
```
使用这个注解
```java
/**
 * CompactDisc实现类
 */
@Component
//@Qualifier("oks")
@Okss
public class AnotherSgtPeppers implements CompactDisc {
    public void sing() {
        System.out.println("AnotherSgtPeppers的sing方法");
    }
}
```

## @Resource:
1. 按照名称（byName）进行装配，名称可以通过name属性进行指定
2. 注解属于J2EE
3. 如果没有指定name属性，当注解写在字段上时，默认取字段名进行按照名称查找，当找不到与名称匹配的bean时才按照类型进行装配(先byName,再byType)
4. 注意:如果name属性一旦指定，就 只会 按照名称进行装配

## @Value:
1. 给基本类型和String类型注入值
2. 可以使用占位符获取属性文件中的值
```java
@Value("${name}")
```

