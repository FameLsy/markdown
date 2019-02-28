---
title: Spring-transaction-manager(事务管理)
date: 2018-02-27 00:00:03
tgas: 
- Spring-transaction-manager(事务管理)
categories: 
- Spring 
---

Spring并不直接管理事务，而是提供了多种事务管理器，他们将事务管理的职责委托给其他相关平台框架的事务来实现。  

Spring事务管理器的接口是PlatformTransactionManager，通过这个接口，Spring为各个平台如JDBC、Hibernate等都提供了对应的事务管理器，但是具体的实现就是各个平台自己的事情了。

# spring框架事务管理的分类

spring事务管理分为两类
1. Spring的编程式事务管理（不推荐使用）：通过手动编写代码的方式完成事务的管理
2. Spring的声明式事务管理（底层采用AOP的技术）：通过一段配置的方式完成事务的管理

## Spring的编程式事务管理（了解）

Spring为了简化事务管理的代码:提供了模板类 TransactionTemplate，所以手动编程的方式来管理事务，只需要使用该模板类即可！！

手动编程方式的具体步骤如下：  

 步骤一:  
 
 配置一个事务管理器，Spring使用PlatformTransactionManager接口来管理事务，所以需要使用到它的实现类！！
```xml
<!-- 配置事务管理器 -->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource"/>
</bean>
```

步骤二:配置事务管理的模板  
```xml
<!-- 配置事务管理的模板 -->
<bean id="transactionTemplate" class="org.springframework.transaction.support.TransactionTemplate">
    <property name="transactionManager" ref="transactionManager"/>
</bean>
```

步骤三:在需要进行事务管理的类中,注入事务管理的模板
```xml
<bean id="accountService" class="com.itheima.demo1.AccountServiceImpl">
    <property name="accountDao" ref="accountDao"/>
    <property name="transactionTemplate" ref="transactionTemplate"/>
</bean>
```

步骤四:在业务层使用模板管理事务:
```java
// 注入事务模板对象
private TransactionTemplate transactionTemplate;
public void setTransactionTemplate(TransactionTemplate transactionTemplate) {
    this.transactionTemplate = transactionTemplate;
}

public void pay(final String out, final String in, final double money) {
    transactionTemplate.execute(new TransactionCallbackWithoutResult() {
        protected void doInTransactionWithoutResult(TransactionStatus status) {
            // 扣钱
            accountDao.outMoney(out, money);
            int a = 10/0;
            // 加钱
            accountDao.inMoney(in, money);
        }
    });
}
```

## 声明式事务管理(重点掌握)

声明式事务管理分为两种方式
1. 基于AspectJ的XML方式（重点掌握）
1. 基于AspectJ的注解方式（重点掌握）

### 基于AspectJ的XML方式实现
