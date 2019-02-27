---
title: Spring-IOC
date: 2018-02-27 00:00:01
tgas: 
- Spring-IOC
categories: 
- Spring 
---

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
# Spring基于注解的使用

与XML方式功能一样,只是soring提供的另一种实现方式

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
1. 作用:  
    把资源让 spring 来管理。相当于在 xml 中配置一个 bean
2. 属性  
    value: 指定 bean 的 id(如果不指定 value 属性，默认 bean 的 id 是当前类的类名。首字母小写)

@Controller、@Service、@Repository注解:属于@Component:的语义化注解，功能一样

### DI注解

