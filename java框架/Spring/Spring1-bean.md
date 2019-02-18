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


# 组件扫描

组件扫描默认是不开启的,但是可以
1. 通过@ComponentScan可以启用组件扫描
2. 通过xml开启

## 通过xml开启

```xml
<context:component-scan base-package="xxx.xxx.xxx">
```

## 对于组建扫描的包设置

方式一：类所在的包就是基础包
```java
@ComponentScan
```

方式二：指定基础包（String 形式）
```java
@ComponentScan(“packageName”)
```

方式三：指定基础包更清晰版（String形式）
```java
@ComponentScan(basePackages = “packageName”)
```
方式四：指定多个基础包（String 形式）
```java
@ComponentScan(backPackages = {“packageName1”, ”packageName2”})
```
方式五：通过类来设置类的所在的包为基础包
```java
@ComponentScan(basePackageClasses={DemoClass.class, DemoClass2.class})
```
>为什么要特地表明2、3、4是string 形式：因为String是类型不安全的（not type-safe）,而5更加安全。5设置可以添加一个空标记接口，只用来设置基础包，这是有利于代码重构的

## 为组件扫描的bean命名

1. 默认
2. 自定义
### 默认命名方式

没有明确命名bean的id，则id为类名首字母小写。如CDPlayer的bean id就是cDplayer

### 自定义命名方式

第一种方式
```java
@Component("cDplayerName")
```

第二种方式

@Name注解来自Java依赖注入规范（Java Dependency Injection），与@Component只有细微差别，大多数情况下可以互换【个人觉得知道就好，没必要用】

```java
@Name("cDplayerName")
```




# 导入和混合配置

## 方式一：javaConfig中导入其他的JavaConfig
通过@Import导入

```java
@Configuration
@Import(CDConfig.class)
public class CDPlayerConfig {
```

## 方式二：JavaConfig中导入XML

通过@ImportResource
```
@Configuration
@ImportResource("classpath:cd-config.xml")
public class CDPlayerConfig {
```

## 方式三：在XML中引入JavaConfig
直接使用< bean >标签

```
    <bean class="com.spring.action.mixedconfig.CDConfig"/>
```

## 方式四：在XML中导入XML

使用< import >标签导入即可

```xml
    <import resource="cd-config.xml"/>
```

## 技巧

甚至一个顶级的配置文件，可以是javaconfig,也可以是xml,将所有的配置全集中在一起，且开启扫描也再次完成。

# 配置文件的加载

## 方式一：自动加载当前类名+context.xml后缀的配置文件。如CDPlayerContext.xml
```java
@ContextConfiguration
```
## 方式二：加载配置类
```java
@ContextConfiguration(classes = JavaConfig.class)
```
## 方式三:加载多个配置类
```java
@ContextConfiguration(classes = {JavaConfig.class, JavaConfig2.class})
```
## 方式四：加载指定的xml文件
```java
@ContextConfiguration(locations=”classpath:xxx.xml”)
```
## 方式五：加载多个指定的xml文件
```java
@ContextConfiguration(locations={“classpath:xxx.xml” , “classpath:xxx2.xml”})
```
>注：用idea开发需要把xml文件放到resources中，idea不会读src下的静态资源文件~~~
