---
title: Spring(2)：注入
date: 2018-02-03 00:00:02
tgas: 
- Spring 注入
categories: 
- Spring 
---

PS：注入其实就是装配，我比较喜欢这种说法，对我而言容易理解。


# Spring 注入
Spring会帮我们完成类的创建，那么，它是如何注入的？
1. 从bean的角度出发，注入可分为通过构造器注入，和通过属性来注入
2. 从类型出发，注入可分为注入字面量和注入bean
3. 比较特殊的，就是@Bean的注入

## 通过构造器来注入

利用构造器方法，有以下几种注入途径
1. 自动注入
2. xml方式注入

### 自动注入

在*构造器方法*上或域上使用了@Autowired标注,会将对应的bean自动注入
```java
{
    //可以将bean装配到域中
    @Autowired
    private CompactDisc compactDisc;
    //或者装配到方法
    @Autowired
    public CDPlayer(CompactDisc compactDisc){
        this.compactDisc = compactDisc;
}
```
>问题：  
>自动注入如何注入字面量

>注：  
>@Autowired可以用在任何方法上，如果没有匹配的bean，会抛出异常  
>@Autowired(required=false)可以避免一场
>可以用@Inject代替

### XML方式注入

方式一：< constructor-arg >标签注入bean和字面量
```xml
 <bean id="cdPlayer" class="com.spring.action.xmlconfig2.CDPlayer">
        <constructor-arg name="compactDisc" ref="sgtPeppers"/>
        <constructor-arg name="name" value="字符串数据"/>
        <constructor-arg name="happy" value="String Data"/>
    </bean>
 ```
   
方式二:C命名空间注册
```xml
    <bean id="cdPlayer" class="com.spring.action.xmlconfig2.CDPlayer"
          c:name="字符串数据"
          c:happy="String Data"
          c:compactDisc-ref="sgtPeppers">
    </bean>
 ```
 ![在这里插入图片描述](https://img-blog.csdn.net/20180911175623166?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01BU09STA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

方式三：C命名空间索引
```xml
    <bean id="cdPlayer" class="com.spring.action.xmlconfig2.CDPlayer"
          c:_0="字符串数据"
          c:_1="String Data"
          c:_2-ref="sgtPeppers">
    </bean>
```
方式四：装配集合

没有使用name，则按构造器参数顺序注入
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="sgtPeppers" class="com.spring.action.xmlconfig3.SgtPeppers"/>

    <bean id="otherCDPlayer" class="com.spring.action.xmlconfig3.OtherCDPlay">
        <!--String title-->
        <constructor-arg value="title"/>
        <!--CompactDisc compactDisc;-->
        <constructor-arg ref="sgtPeppers"/>
        <!--List<String> stringList;-->
        <constructor-arg>
            <list>
                <value>a</value>
                <value>b</value>
                <value>c</value>
                <value>d</value>
            </list>
        </constructor-arg>
        <!--List<CompactDisc> compactDiscList;-->
        <constructor-arg>
            <list>
                <ref bean="sgtPeppers"/>
                <ref bean="sgtPeppers"/>
                <ref bean="sgtPeppers"/>
                <ref bean="sgtPeppers"/>
            </list>
        </constructor-arg>
        <!--Set<String> compactDiscs;-->
        <constructor-arg>
            <set>
                <value>a</value>
                <value>b</value>
                <value>c</value>
                <value>d</value>
            </set>
        </constructor-arg>
        <!--Set<CompactDisc> compactDiscSet;-->
        <constructor-arg>
            <set>
                <ref bean="sgtPeppers"/>
            </set>
        </constructor-arg>
    </bean>

</beans>
```

## 属性注入

如果需要注入的类只有无参构造方法，那么就不能使用构造器来注入全部属性，则需要提供setter方法来进行属性注入

### 通过< property >  
```xml
    <bean id="cdPlayer" class="com.spring.action.xmlconfig4.CDPlayer">
        <property name="name" value="name"/>
        <property name="happy" value="happy"/>
        <property name="compactDisc" ref="sgtPeppers"/>
    </bean>
```

### p命名空间法注入
```xml
    <bean id="cdPlayer" class="com.spring.action.xmlconfig4.CDPlayer"
          p:name="name"
          p:happy="happy"
          p:compactDisc-ref="sgtPeppers"
    />
```
![在这里插入图片描述](https://img-blog.csdn.net/20180911203903801?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01BU09STA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 注入个集合
```xml
    <bean id="otherCDPlayer" class="com.spring.action.xmlconfig4.OtherCDPlay">
        <property name="title" value="title"/>
        <property name="compactDisc" ref="sgtPeppers"/>
        <property name="stringList">
            <list>
                <value>a</value>
                <value>b</value>
                <value>c</value>
                <value>d</value>
            </list>
        </property>
        <property name="compactDiscList" >
            <list>
                <ref bean="sgtPeppers"/>
                <ref bean="sgtPeppers"/>
                <ref bean="sgtPeppers"/>
                <ref bean="sgtPeppers"/>
            </list>
        </property>
        <property name="compactDiscs">
            <set>
                <value>a</value>
                <value>b</value>
                <value>c</value>
                <value>d</value>
            </set>
        </property>
        <property name="compactDiscSet">
            <set>
                <ref bean="sgtPeppers"/>
            </set>
        </property>
    </bean>
```

## p命名空间结合utli空间来注入集合

p命名空间是无法注入集合的，所有需要结合utli空间来注入集合
```xml
    <util:list id="stringList">
        <value>a</value>
        <value>b</value>
        <value>c</value>
        <value>d</value>
    </util:list>

    <util:list id="compactDiscList">
        <ref bean="sgtPeppers"/>
        <ref bean="sgtPeppers"/>
        <ref bean="sgtPeppers"/>
        <ref bean="sgtPeppers"/>
    </util:list>

    <util:set id="compactDiscs">
        <value>a</value>
        <value>b</value>
        <value>c</value>
        <value>d</value>
    </util:set>

    <util:set id="compactDiscSet">
        <ref bean="sgtPeppers"/>
    </util:set>

    <bean id="otherCDPlayer" class="com.spring.action.xmlconfig4.OtherCDPlay"
          p:title="title"
          p:compactDisc-ref="sgtPeppers"
          p:stringList-ref="stringList"
          p:compactDiscList-ref="compactDiscList"
          p:compactDiscs-ref="compactDiscs"
          p:compactDiscSet-ref="compactDiscSet"
    />
```

Spring util-命名空间中的元素
|元素|描 述|
|--|--|
|util:constant	|引用某个类型的public static域，并将其暴露为bean|
|util:list	|创建一个java.util.List类型的bean，其中包含值或引用|
|util:map|	创建一个java.util.Map类型的bean，其中包含值或引用|
|util:properties|	创建一个java.util.Properties类型的bean|
|util:property-path|	引用一个bean的属性（或内嵌属性），并将其暴露为bean|
|util:set|	创建一个java.util.Set类型的bean，其中包含值或引用|



# 处理自动装配歧义
一般情况下，都是通过接口来注入bean，当一个接口有N个对应的Bean时，就会发生歧义  

解决办法有：
1. 标示首选的bean处理歧义
2. 限定范围

## 标示首选的bean处理歧义

使用 @Primary标签
```java
@Component
@Primary
public class xxx
//或者
@Bean
@Primary
public XXX getXXX(){}
```

或者通过xml来标志首选bean【不推荐，自动装配还是用注解吧，这样有点乱】
```xml
    <bean id="otherSgtPeppers" class="com.spring.ambiguity.xmlAmbiguity.OtherSgtPeppers" primary="true"/>
```

当首选bean不止一个的时候，依旧会出现歧义

## 限定范围

通过与@Autowired或@Inject协同使用,可以指定注入的bean的id
```java
@Autowire
@Qualifier("bean_id")
...
```
对应bean id，可以通过@Qualifier和@Compontent联合使用，达到自定义bean的名称
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

```

```
### 2.3.4 自定义一个注解
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
测试
```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = QuaifierConfig.class)
public class QuaifierConfigTest {
    @Autowired
//    @Qualifier("anotherSgtPeppers")
    @Okss
    private CompactDisc compactDisc;

    @Autowired
    private MediaPlayer mediaPlayer;

    @Test
    public void testProfiles(){
        compactDisc.sing();
    }
}
```