---
title: Spring-IOC
date: 2018-02-27 00:00:01
tgas: 
- Spring-IOC
categories: 
- Spring 
---
知识点:
1. 概念
2. IOC 容器的创建
3. IOC bean 的相关操作
4. IOC 依赖注入
5. 常用注解

# 什么是IOC？  

所谓的 IoC容器 就是指的Spring中Bean工厂里面的Map存储结构（存储了Bean的实例）

# 创建IOC容器

1. 在java应用中创建IOC容器
2. 在Web应用中创建IOC容器

## 在java应用中创建IOC容器
```java
ApplicationContext context = new ClassPathXmlApplicationContext(xml路径);
```

## 在Web应用中创建IOC容器

web.xml中配置ContextLoaderListener接口，并配置ContextConfigLocation参数


# IOC - MAVEN
使用Spring-IOC需要的包
```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>5.0.7.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.0.7.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-beans</artifactId>
    <version>5.0.7.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-expression</artifactId>
    <version>5.0.7.RELEASE</version>
</dependency>
```

# Spring 基于XML的使用

XML方式的实现，适合于来自第三方，使用其它框架时使用

## Spring IOC基于XML的创建实例对象

在Spring 的XML配置文件中配置一个bean标签，该标签最终会被加载为一个BeanDefition对象（描述对象信息）

```xml
<bean id="" class=""></bean>
```

bean标签详解

作用：
1. 用于配置对象让 spring 来创建的
2. 默认情况下它调用的是类中的无参构造函数。如果没有无参构造函数则不能创建成功

属性:
1. id:唯一标识
2. class:指定类的全限定类名。用于反射创建对象。默认情况下调用无参构造函数
3. scope指定对象的作用范围[1]
4. init-method：指定类中的初始化方法名称
5. destroy-method：指定类中销毁方法名称。比如DataSource的配置中一般需要指定destroy-method=“close”
5. factory-method:指定生产对象的静态方法(用于通过静态工厂实现bean的实例化)

>注[1]  
>scope的作用范围  
>singleton :默认值，单例的（在整个容器中只有一个对象）.
>prototype :多例  
>request:Web 项目中,Spring 创建一个 Bean 的对象,将对象存入到 request 域中  
>session:Web 项目中,Spring 创建一个 Bean 的对象,将对象存入到 session 域中  
>global session	:Web 项目中,应用在 Portlet 环境.如果没有 Portlet 环境那么globalSession 相当于 session.

bean实例化的三种方式

讲述bean是如何通过Spring进行实例化的

1. 使用默认无参构造函数(重点)
2. 静态工厂(了解)
3. 实例工厂(了解)

### 使用默认无参构造函数

默认情况，如果bean对应的class没有无参构造函数，就会报错

```java
<bean id="" class=""/>
```

>注  
>如果不指定id,那么bean的 id将会是类的全名
> 如class="com.liisyu.dao.impl.UserDaoImpl"
>那么bean的默认id="com.liisyu.dao.impl.UserDaoImpl"，而不是userDaoImpl

### 静态工厂

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

### 实例工厂

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
再调用实力工厂的创建方法
```xml
<bean id="" factory-bean="instancFactory" factory-method="createUserService"></bean>
```
## Spring DI(依赖注入)基于XML的使用

依赖：  
指的就是Bean实例中的属性：简单类型（8种基本类型和String类型）的属性、POJO类型的属性、集合数组类型的属性。

依赖注入:  
 spring 框架核心 ioc 的具体实现，ioc实现控制反转，将对象的创建交给spring。而对象的必定存在依赖，需要通过注入的方式获取依赖（Spring来实现注入）。

注入方式
1. 构造器注入
2. setter方法注入

### 通过构造器来注入

使用< constructor-arg >标签
```xml
<bean id="" class="">
    <constructor-arg/>
</bean>
```

< constructor-arg >属性:
1. index:指定参数在构造函数参数列表的索引位置
2. name:指定参数在构造函数中的名称
3. value:它能赋的值是基本数据类型和 String 类型
4. ref:它能赋的值是其他 bean 类型，也就是说，必须得是在配置文件中配置过的 bean

注入简单类型
```xml
<constructor-arg name="name" value="liisyu"/>
<constructor-arg name="number" value="123"/>
```

注入bean类型
```xml
<constructor-arg name="user" ref="user"/>
```

注入集合类型
```xml
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
        <constructor-arg name="map">
          <map>
            <entry key="number" value="38"/>
            <entry key="number2" value="38"/>
            <entry key="number3" value="29"/>
        </map>
        </constructor-arg>
        <constructor-arg name="map">
          <map>
            <entry key="user" value-ref="user"/>
            <entry key="user2" value-ref="user"/>
            <entry key="user3" value-ref="user"/>
        </map>
        </constructor-arg>
        <!-- Properties集合 -->
        <constructor-arg name="pro">
            <props>
                <prop key="uname">root</prop>
                <prop key="pass">123</prop>
            </props>
        </constructor-arg>
    </bean>
```

此外，构造函数还有一种简写方式，C命名空间索引(无法注入集合，也不推荐使用)

```xml
<!-- 按属性的顺序传入参数 -->
    <bean id="cdPlayer" class="com.spring.action.xmlconfig2.CDPlayer"
          c:_0="字符串数据"
          c:_1="123"
          c:_2-ref="user">
    </bean>
```
>注意  
>既然是通过构造器注入的，那必须要有个有参构造器

### 通过setter方法注入

setter注入方式
1. 手动装配方式注入(XML方式)：< bean>标签的子标签< property >，需要在类中指定setter方法
2. 自动装配方式注入(注解方式,再基于注解的文章说明)

< property >的使用
```xml
    <bean id="" class="">
        <!-- 注入简单类型 -->
        <property name="name" value="name"/>
        <property name="number" value="123"/>
        <!-- 注入bean类型 -->
        <property name="user" ref="user"/>
        <!-- 注入集合 -->
         <!--List<String> stringList;-->
        <property name="stringList">
            <list>
                <value>a</value>
                <value>b</value>
                <value>c</value>
                <value>d</value>
            </list>
        </property>
          <!--List<CompactDisc> compactDiscList;-->
        <property name="compactDiscList" >
            <list>
                <ref bean="sgtPeppers"/>
                <ref bean="sgtPeppers"/>
                <ref bean="sgtPeppers"/>
                <ref bean="sgtPeppers"/>
            </list>
        </property>
        <!--Set<String> compactDiscs;-->
        <property name="compactDiscs">
            <set>
                <value>a</value>
                <value>b</value>
                <value>c</value>
                <value>d</value>
            </set>
        </property>
        <!--Set<CompactDisc> compactDiscSet;-->
        <property name="compactDiscSet">
            <set>
                <ref bean="sgtPeppers"/>
            </set>
        </property>
        <property name="map">
          <map>
            <entry key="number" value="38"/>
            <entry key="number2" value="38"/>
            <entry key="number3" value="29"/>
        </map>
        </property>
            <property name="map">
          <map>
            <entry key="user" value-ref="user"/>
            <entry key="user2" value-ref="user"/>
            <entry key="user3" value-ref="user"/>
        </map>
        </property>
        <!-- Properties集合 -->
        <property name="pro">
            <props>
                <prop key="uname">root</prop>
                <prop key="pass">123</prop>
            </props>
        </property>
    </bean>
```

简化使用，p命名空间(无法注入集合)

![在这里插入图片描述](https://img-blog.csdn.net/20180911203903801?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01BU09STA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

```xml
    <bean id="" class=""
          p:name="name"
          p:number="123"
          p:compactDisc-ref="user"
    />
```
### 解决命名空间无法注入集合的方式
使用< util >标签(需要加约束),如
```xml
创建了一个集合的对象
    <util:list id="stringList">
        <value>a</value>
        <value>b</value>
        <value>c</value>
        <value>d</value>
    </util:list>

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

### 加载Properties文件
```xml
 <context:property-placeholder location="classpath:foo.properties" />
```

## Spring 配置混合使用

### 在XML中引入JavaConfig
直接使用< bean >标签

```
    <bean class="com.spring.action.mixedconfig.CDConfig"/>
```

### 在XML中导入XML

使用< import >标签导入即可

```xml
    <import resource="cd-config.xml"/>
```

### xml中定义profile

只有激活profile才会使用到这个配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd"
       profile="dev">
```

可以使用嵌套beans的方式，而不是为为每一个环境设置一个profile

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd"
       profile="dev">

       <beans profile="xxx"></beans>
       <beans profile ="sdsd"></beans>
```
Spring 激活profile主要依赖两个属性
1. spring.profiles.active
2. spring.profiles.default


有多种方式来设置这两个属性
1. 作为DispatcherServlet的初始化参数；
2. 作为Web应用的上下文参数；
3. 作为JNDI条目；
4. 作为环境变量；
5. 作为JVM的系统属性；
6. 在集成测试类上，使用@ActiveProfiles注解设置。

第一种方式示例
```xml
<!-- web.xml -->
<!-- 为上下文设置默认的profile -->
    <context-param>
        <param-name>spring.profiles.default</param-name>
        <param-value>/WEN-INF/spring/root-context.xml</param-value>
    </context-param>

    <servlet>
        ...
        <init-param>
            <param-name>spring.profiles.default</param-name>
            <param-value>dev</param-value>
        </init-param>
    </servlet>
```

# Spring基于注解的使用

与XML方式功能一样,只是soring提供的另一种实现方式，更加适用于bean的实现类由用户自己开发的场景

## 开启注解扫描
想使用注解，第一步，开启注解扫描,开启可以使用xml方式，也可以使用注解方式

### 使用xml开启注解
```xml
<!-- base-package传入如，com.liisyu.spring.service,那么它就会扫描该类所在的包 -->
<context:component-scan base-package="" />
```

### 使用java开启注解
通过@ComponentScan来开启,一般配合@Configuration注解使用
```java
@Configuration
@ComponentScan(basePackages="com.liisyu.spring.service")
public class SpringConfiguration(){
    ...
}
```

## 常用注解

开启完注解后，介绍一下常用的注解

### IOC 注解

效果相当于< bean >标签，主要由
1. @Component:
2. @Controller:一般用于表现层的注解
3. @Service:一般用于业务层的注解
4. @Repository:一般用于持久层的注解

@Component:  
1. 通过value属性 指定 bean 的 id(如果不指定 value 属性，默认 bean 的 id 是当前类的类名。首字母小写)

@Controller、@Service、@Repository注解:属于@Component:的语义化注解，功能一样

### DI注解

效果相当于< property >标签,主要有
1. @Autowired
2. @Qualifier
3. @Resource
4. @Value

@Autowired:
1. 默认按类型装配（byType）
2. 这个注解是spring自身的
3. 默认情况下必须要求依赖对象必须存在,如果要允许null值，可以设置@Autowired(required=false)
4. 可以结合@Qualifier注解进行名称装配

@Qualifier:
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

@Resource:
1. 按照名称（byName）进行装配，名称可以通过name属性进行指定
2. 注解属于J2EE
3. 如果没有指定name属性，当注解写在字段上时，默认取字段名进行按照名称查找，当找不到与名称匹配的bean时才按照类型进行装配(先byName,再byType)
4. 注意:如果name属性一旦指定，就 只会 按照名称进行装配

@Value:
1. 给基本类型和String类型注入值
2. 可以使用占位符获取属性文件中的值
```java
@Value("${name}")
```

### @Scope

效果相当于
```xml
< bean id="" class="" scope="">
```
只有一个注释:@Scope,用法如下
```java
@Scope(value="singleton")
```

### 生命周期相关

效果相当于
```xml
<bean id="" class="" init-method="" destroy-method="" />
```

有两个注解
1. @PostConstruct
2. @PreDestroy

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

### @Configuration 注解

从Spring3.0开始使用，用于定义配置类，可替换xml文件，效果相当于< beans >根标签

当配置类内部包含一个或多个@Bean注解的方法时，这些方法将会被AnnotationConfigApplicationContext或AnnotationConfigWebApplicationContext类进行扫描，并用于构建bean定义，初始化Spring容器。

```java
@Configuration
public class SpringConfiguration(){
    ...
}
```

不同方式的@ComponentScan扫描路径

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
### @Bean注解

效果相当于< bean >标签，通过标注在返回实例的方法上，将返回的实例注册为bean(主要用于注册第三方的bean)

属性:
1. name:给当前注解方法创建的对象指定一个名称(即 bean 的 id）
2. 不指定name,默认与标注的方法名相同
3. 注解默认作用域为单例singleton作用域，可通过@Scope()设置域；

```java
@Bean
@Scope("prototype")
public UserService userService(){
    return new UserServiceImpl();
}
```
### @Primary

效果相当于
```xml
    <bean id="" class="" primary="true"/>
```
标示首选的bean处理歧义

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

### @ComponentScan

效果相当于< context:component-scan >标签,扫描@Component、@Controller、@Service、@Repository注解的类。

属性:
1. basePackages：用于指定要扫描的包
2. value：和basePackages作用一样

### @PropertySource

效果相当于< context:property-placeholder >标签，加载properties配置文件

属性：
1. value[]: 用于指定properties文件路径，如果在类路径下，需要写上classpath

```java
@Configuration
@PropertySource(“classpath:jdbc.properties”)
public class JdbcConfig {
    @Value("${jdbc.driver}")
    private String driver;
    @Value("${jdbc.url}")
    private String url;
```

### @Import /@ImportResource

用来组合多个配置类或配置文件

混合导入的方式：

方式一：javaConfig中导入其他的JavaConfig,在引入其他配置类时，可以不用再写@Configuration 注解
通过@Import导入

```java
@Configuration
@Import(CDConfig.class)
public class CDPlayerConfig {
```

方式二：JavaConfig中导入XML

通过@ImportResource
```
@Configuration
@ImportResource("classpath:xxx/xxx/cd-config.xml")
public class CDPlayerConfig {
```
### 获取容器注解

Java应用：  
使用AnnotationConfigApplicationContext类获取容器
```java
ApplicationContext context = 
new AnnotationConfigApplicationContext(SpringConfiguration.class);

UserService service = context.getBean(UserService.class);
service.saveUser();
```

Web应用:
使用AnnotationConfigWebApplicationContext类获取容器 

@TODO

### @ContextConfiguration
配置文件的加载,主要由以下几种方式

方式一：自动加载当前类名+context.xml后缀的配置文件。如CDPlayerContext.xml
```java
@ContextConfiguration
```
方式二：加载配置类
```java
@ContextConfiguration(classes = JavaConfig.class)
```
方式三:加载多个配置类
```java
@ContextConfiguration(classes = {JavaConfig.class, JavaConfig2.class})
```
方式四：加载指定的xml文件
```java
@ContextConfiguration(locations=”classpath:xxx/xxx.xml”)
```
方式五：加载多个指定的xml文件
```java
@ContextConfiguration(locations={“classpath:xxx/xxx.xml” , “classpath:xxx/xxx2.xml”})
```
>注：用idea开发需要把xml文件放到resources中，idea不会读src下的静态资源文件~~~

### @Profile/ActiveProfiles

通过激活不同的profile，达到加载不同的bean

基于类级别的profile
```java
@Profile("dev")
public class ClassprofilesConfig {
    @Bean
    public CompactDisc compactDisc(){
        return new SgtPeppers();
    }
```
基于方法级别的profile
```java
@Bean 
@Profile("dev")
public CompactDisc compactDisc(){
    return new SgtPeppers();
}
```

利用@ActiveProfiles注解来激活某个profile
```
@ActiveProfiles("dev")
```

>注意：@profile本质上是使用了@Conditional来创建的注解

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


