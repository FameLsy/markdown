---
title: Spring-IOC-条件化的bean
tags: 
- Spring-IOC-条件化的bean
categories: 
- Spring 
---

# 条件化的bean

利用@Conditional注解来配合@Bean使用，满足@Conditional并返回true时才会创建bean  

使用@Conditional(XXX.class),使用XXX.class来设置@Conditional
```java
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


