---
title: 依赖注入
tags: 
- 依赖注入
categories: 
- Spring 
---

依赖：  
指的就是Bean实例中的属性：简单类型（8种基本类型和String类型）的属性、POJO类型的属性、集合数组类型的属性。

依赖注入:  
 spring 框架核心 ioc 的具体实现，ioc实现控制反转，将对象的创建交给spring。而对象的必定存在依赖，需要通过注入的方式获取依赖（Spring来实现注入）。

注入方式的方式有两种
1. 构造器注入
2. setter方法注入

# 通过构造器来注入

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

# 通过setter方法注入

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
