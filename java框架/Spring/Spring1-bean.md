---
title: Spring(1)：bean
date: 2018-02-03 00:00:01
tgas: 
- Spring bean
categories: 
- Spring 
---


# Spring定义bean的方式
Spring主要是通过将类创建为bean，在操作bean来实现各种功能，那么，如何姜磊定义bean呢?
1. 通过@Component注解定义bean
2. 通过xml文件来定义bean
3. 通过java方式来定义bean

## 通过@Component注解来定义bean

在需要创建bean的类上使用@Component注解，使用@Component注解，表示组件类，spring 会为其创建bean

```java
@Component
public class DemoClass{...}
```
当然，为了能够使用组件类，必须扫描获取这个组件类，所有需要开启组建扫描

>注  
> 我在官方文档看到对Component类的解释  
>@Component是任何Spring管理组件的通用构造型  
>由此猜想:java所有操作都是依靠bean来实现的

## XML方式设置bean
对于xml来定义bean，只需要使用< bean>标签就可以
xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="demoClass" class="xxx.xxx.DemoClass"/>
</beans>
```

## 利用@Bean方式定义bean

1. 通过@Bean，方法返回的实例会被Spring作为bean保存
2. 对于有参数的方法，@Bean 注解的方法会自动将bean注入到参数中
3. 通过@Bean(name="")来自定义bean名
```java
@Configuration
public class JavaConfig {
    //为sgtPeppers创建bean
    @Bean(name="sgtperresss")
    public CompactDisc compactDisc(){
        return new SgtPeppers();
    }
    //为DCPlayer创建bean
    @Bean
    public MediaPlayer mediaPlayer(CompactDisc compactDisc){
        return new CDPlayer(compactDisc);
    }
}
```


# 条件化的bean

利用@Conditional注解来配合@Bean使用，满足@Conditional并返回true时才会创建bean  

使用@Conditional(XXX.class),使用XXX.class来设置@Conditional
```java
//
   @Bean
    @Conditional(MagciExistsCondition.class)
    public Magic magic() {
        return new MagicBean();
    }
```
设置@Conditional的类，需要实现condition接口，重写matches方法
1. 方法return ture,则创建bean
2. 否则，不创建bean
```
public class MagciExistsCondition implements Condition {
    public boolean matches(ConditionContext conditionContext, AnnotatedTypeMetadata annotatedTypeMetadata) {
      return true;
     // return false;
    }
}
```

## matches方法的第一个参数ConditionContext
源码如下
```java
public interface ConditionContext {

//通过返回的BeanDefinitionRegistry检查bean定义
    BeanDefinitionRegistry getRegistry();

//通过返回的ConfigurableListableBeanFactory检查bean是否存在，甚至探查bean的属性
    ConfigurableListableBeanFactory getBeanFactory();

//)通过返回的Environment检查环境变量是否存在以及它的值是什么；
    Environment getEnvironment();

//通过返回的ResourceLoader所加载的资源；
    ResourceLoader getResourceLoader();

//通过返回的ClassLoader加载并检查类是否存在。
    ClassLoader getClassLoader();
}
```
## matches方法的第二个参数AnnotatedTypeMetadata
源码如下
```java
public interface AnnotatedTypeMetadata {

//检查带有@Bean注解的方法是不是还有其他特定的注解
    boolean isAnnotated(String var1);

//其余的都是用来获取其他注解的属性
    Map<String, Object> getAnnotationAttributes(String var1);

    Map<String, Object> getAnnotationAttributes(String var1, boolean var2);

    MultiValueMap<String, Object> getAllAnnotationAttributes(String var1);

    MultiValueMap<String, Object> getAllAnnotationAttributes(String var1, boolean var2);
}
```

## @profile 和 @Conditional

@Profile注解其实是实现了Conditional注解，且传入的实例为ProfileCondition。

```java
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by Fernflower decompiler)
//

package org.springframework.context.annotation;

import java.util.Iterator;
import java.util.List;
import org.springframework.core.type.AnnotatedTypeMetadata;
import org.springframework.util.MultiValueMap;

class ProfileCondition implements Condition {
    ProfileCondition() {
    }

    public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
        if (context.getEnvironment() != null) {
            MultiValueMap<String, Object> attrs = metadata.getAllAnnotationAttributes(Profile.class.getName());
            if (attrs != null) {
                Iterator var4 = ((List)attrs.get("value")).iterator();

                Object value;
                do {
                    if (!var4.hasNext()) {
                        return false;
                    }

                    value = var4.next();
                } while(!context.getEnvironment().acceptsProfiles((String[])((String[])value)));

                return true;
            }
        }

        return true;
    }
}
```