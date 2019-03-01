---
title: Spring-Mybatis
date: 2018-02-27 00:00:04
tgas: 
- Spring-Mybatis
categories: 
- Spring 
---

对于整合第三方框架，使用xml方式更为适合

# 整合思路
Spring的责任：
1. Spring在Java项目中，主要的责任就是对JavaBean进行IOC处理
2. 对于持久层  
    DataSource:数据源
    SqlSessionFactory(单例管理):Mybaits对象
    Mapper对象
3. 对于业务层
    Service实现类
    事务管理

那么，只要针对以上bean，就可以整合Mybatis

# 整合实现

## 持久层配置(application-dao.xml)

需要配置如下
1. 数据源的配置
2. SqlSessionFatory的配置
3. Mapper代理对象的配置(因为Mapper在mybatis中只是一个接口，没有实现类，所以配置的应是它的代理对象)  
    方式一：MapperFactoryBean  
    方式二：MapperScannerConfigurer  

### 配置数据源
```xml
<!--读入properties文件-->
<context:property-placeholder location="classpath:spring/spring-application.xml"/>

<!--配置数据库源-->
<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource">
    <property name="driverClassName" value="${db.driverClassName}"/>
    <property name="url" value="${db.url}"/>
    <property name="username" value="${db.username}"/>
    <property name="password" value="${db.password}"/>
</bean>
```

### 配置SqlSessionFactory
```xml
<!--配置SqlSessionFactory-->
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <!--注入dataSoure-->
    <property name="dataSource" ref="dataSource"/>
</bean>
```

在这里，使用的是Spring提供的SqlSessionFactoryBean类，它的作用如下
1. 创建一个sqlSessionFactory
2. 代替Mybatis的配置文件
3. 如果需要加载Mybatis配置文件，也提供了相应的属性

如下,Mybatis配置文件，可以省略
```xml
<!-- 可以省略 -->
<property name="configLocation" value="mybatis/SqlMapConfig.xml"/>
```
或者,批量设置别名
```xml
<!-- mybatis批量别名配置 -->
<property name="typeAliasesPackage" value="com.liisyu.pojo"/>
```

Spring要比Mybatis更加擅长管理数据源

### 配置Mapper代理对象

Mapper在Mybatis是接口形式存在，相当于Dao接口

方式一：MapperFactoryBean  
缺点是一次只能针对一个接口生成代理对象
```xml
<bean id="accountMapper" class="org.mybatis.spring.mapper.MapperFactoryBean">
    <!--注入SqlSessionFactory-->
    <property name="sqlSessionFactory" ref="sqlSessionFactory"/>
    <!--注入目标接口类-->
    <property name="mapperInterface" value="com.liisyu.mapper.AccountMapper"/>
</bean>
```

方式二:使用MapperScannerConfigurer  
能批量生成代理对象
```xml
<!-- 批量代理对象的生成 -->
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <!-- 指定需要生成代理的接口所在的包名 -->
    <property name="basePackage" value="com.liisyu.mapper"/>
</bean>
```

### 总结

配置完成后的application-dao.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
           http://www.springframework.org/schema/context
           http://www.springframework.org/schema/context/spring-context.xsd">

    <!--读入properties文件-->
    <context:property-placeholder location="classpath:db.properties"/>

    <!--配置数据库源-->
    <bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource">
        <property name="driverClassName" value="${db.driverClassName}"/>
        <property name="url" value="${db.url}"/>
        <property name="username" value="${db.username}"/>
        <property name="password" value="${db.password}"/>
    </bean>

    <!--配置SqlSessionFactory-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!--注入dataSoure-->
        <property name="dataSource" ref="dataSource"/>
        <!-- mybatis批量别名配置 -->
        <property name="typeAliasesPackage" value="com.liisyu.pojo"/>
    </bean>

    <!--配置Mapper代理对象-->
    
    <!--方式二：使用MapperScannerConfigurer-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!-- 指定需要生成代理的接口所在的包名 -->
        <property name="basePackage" value="com.liisyu.mapper"/>
    </bean>
</beans>
```

## 业务层配置

### Service配置(application-service.xml)

业务层配置很简单，只需要开启扫描业务层即可
```xml
<context:component-scan base-package="com.liisyu.service"/>
</beans>
```

### 事务配置(application-tx.xml)

主要是以事务通知方式配置，详细看Spring事务管理
```xml
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource"/>
</bean>

<tx:advice id="txAdvice" transaction-manager="transactionManager">
    <!-- 设置事务管理信息 -->
    <tx:attributes>
        <tx:method name="transfer*" propagation="REQUIRED"
                    isolation="DEFAULT" />
    </tx:attributes>
</tx:advice>

<aop:config>
    <aop:advisor advice-ref="txAdvice"
                    pointcut="execution(* *..*.*ServiceImpl.*(..))" />
</aop:config>
```

## 类文件的创建
配置完后，接下来就是配置一些基本类了，主要有
1. POJO类
2. Mapper接口
3. 业务层的Service类

POJO类
```java
package com.liisyu.pojo;

/**
 * @author liisyu
 * @date 2019/3/1
 */
public class Account  {
    private String name;
    private long id;
    private double money;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public long getId() {
        return id;
    }

    public void setId(long id) {
        this.id = id;
    }

    public double getMoney() {
        return money;
    }

    public void setMoney(double money) {
        this.money = money;
    }

    @Override
    public String toString() {
        return "Account{" +
                "name='" + name + '\'' +
                ", id=" + id +
                ", money=" + money +
                '}';
    }
}
```

Mapper接口,其实相当于Dao接口
```java
public interface AccountMapper {
    void updateMoney(@Param("name") String name, @Param("money") double money);
    double queryMoney(String name);
}
```

同时，通常在Mapper接口同路径下，配置一个映射文件
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.liisyu.mapper.AccountMapper">
    <!--查询-->
    <select id="queryMoney" parameterType="string" resultType="double">
        SELECT money FROM t_account WHERE name = #{name}
    </select>
    <!--修改-->
    <update id="updateMoney" parameterType="map">
        UPDATE t_account SET money = #{money} WHERE name = #{name}
    </update>
</mapper>
```
>注意!!  
>Mapper接口和映射文件必须同名!  
>AccountMapper.java  
>AccountMapper.xml  

Service接口
```java
public interface AccountService {
    void transfer(String in, String out, double money);
}
```

Service实现  
注入了映射器接口的代理对象
```java
import javax.annotation.Resource;

/**
 * @author liisyu
 * @date 2019/3/1
 */
@Service(value = "accountService")
public class AccountServiceImpl implements AccountService {
// 注入映射器代理对象
    @Resource(name = "accountMapper")
    private AccountMapper accountMapper;

    public void setAccountDao(AccountMapper accountMapper) {
        this.accountMapper = accountMapper;
    }

    /**
     * 用于模拟转账
     *
     * @param out   转出账户
     * @param in    转入账户
     * @param money 转移钱数
     */
    @Override
    public void transfer(String out, String in, double money) {
        //查询钱
        double aMoney = accountMapper.queryMoney(out);
        //扣钱
        accountMapper.updateMoney(out, aMoney - money);

//                System.out.println(1/0);
        //查询钱
        aMoney = accountMapper.queryMoney(in);
        //加钱
        accountMapper.updateMoney(in, aMoney + money);
    }
}
```
测试
```java
 @Resource
    AccountService accountService;

    @Test
    public void test(){
        accountService.transfer("wzc","liisyu",100);
    }
```
