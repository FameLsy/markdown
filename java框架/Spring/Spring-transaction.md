---
title: Spring-transaction-manager(事务管理)
date: 2018-02-27 00:00:04
tgas: 
- Spring-transaction-manager(事务管理)
categories: 
- Spring 
---

Spring并不直接管理事务，而是提供了多种事务管理器，他们将事务管理的职责委托给其他相关平台框架的事务来实现。  

Spring事务管理器的接口是PlatformTransactionManager，通过这个接口，Spring为各个平台如JDBC、Hibernate等都提供了对应的事务管理器，但是具体的实现就是各个平台自己的事情了。

# Spring框架的事务管理相关的类和API

![Spring事务管理接口](https://raw.githubusercontent.com/FameLsy/Images/master/spring/Spring%E4%BA%8B%E5%8A%A1%E7%AE%A1%E7%90%86%E6%8E%A5%E5%8F%A3.png)

介绍：
1. PlatformTransactionManager接口:平台事务管理器.(真正管理事务的类)。该接口有具体的实现类，根据不同的持久层框架，需要选择不同的实现类！
2. TransactionDefinition接口：事务定义信息（包括事务的隔离级别,传播行为,超时,只读等）
3. TransactionStatus接口：事务的状态（包括是否新事务、是否已提交、是否有保存点、是否回滚）
4. 关系：PlatformTransactionManager真正管理事务对象.根据事务定义的信息TransactionDefinition 进行事务管理，在管理事务中产生一些状态.将状态记录到TransactionStatus中

## PlatformTransactionManager接口

实现类:
1. DataSourceTransactionManager实现类:适用Spring的JDBC模板或者MyBatis（IBatis）框架
2. HibernateTransactionManager实现类:适用Hibernate的框架

常用方法:
1. void commit(TransactionStatus status) 
2. TransactionStatus getTransaction(TransactionDefinition definition) 
3. void rollback(TransactionStatus status) 

## TransactionDefinition

包含的事务隔离级别的常量：
1.  static int ISOLATION_DEFAULT（采用数据库的默认隔离级别）
2. static int ISOLATION_READ_UNCOMMITTED 
3. static int ISOLATION_READ_COMMITTED 
4. static int ISOLATION_REPEATABLE_READ 
5. static int ISOLATION_SERIALIZABLE 

事务的传播行为：解决的是业务层之间的方法调用

事务的传播行为常量（不用设置，使用默认值，以A调用B为例）：
1. PROPAGATION_REQUIRED（默认值） -- A中有事务,使用A中的事务.如果没有，B就会开启一个新的事务,将A包含进来.(保证A,B在同一个事务中)，默认值！！
2. PROPAGATION_SUPPORTS          -- A中有事务,使用A中的事务.如果A中没有事务.那么B也不使用事务.
3. PROPAGATION_MANDATORY         -- A中有事务,使用A中的事务.如果A没有事务.抛出异常.
4. PROPAGATION_REQUIRES_NEW      -- A中有事务,将A中的事务挂起.B创建一个新的事务.(保证A,B没有在一个事务中)
5. PROPAGATION_NOT_SUPPORTED     -- A中有事务,将A中的事务挂起.
6. PROPAGATION_NEVER             -- A中有事务,抛出异常.
7. PROPAGATION_NESTED            -- 嵌套事务.当A执行之后,就会在这个位置设置一个保存点.如果B没有问题.执行通过.如果B出现异常,运行客户根据需求回滚(选择回滚到保存点或者是最初始状态)


# spring框架事务管理的分类

spring事务管理分为两类
1. Spring的编程式事务管理（不推荐使用）：通过手动编写代码的方式完成事务的管理
2. Spring的声明式事务管理（底层采用AOP的技术）：通过一段配置的方式完成事务的管理

## Spring的编程式事务管理（了解）

类图
1. 持久层：实现JdbcDaoSupport，使用Spring提供的JdbcTemplate模板类实现JDBC操作
2. 业务层：配置了TransactionTemplate类，该类是Spring用来简化事务管理的模板类

![tran1](https://raw.githubusercontent.com/FameLsy/Images/master/spring/tran1.png)

持久层
```java
public interface AccountDao {
    void updateMoney(String name, double money);
    double queryMoney(String name);
}
/**
 * 持久层，DAO实现类
 * @author liisyu
 * @date 2019/3/1
 */
public class AccountDaoImpl extends JdbcDaoSupport implements AccountDao {
    /**
     * 更新钱
     * @param name
     * @param money
     */
    @Override
    public void updateMoney(String name, double money) {
        this.getJdbcTemplate().update("UPDATE t_account SET money = ? WHERE name = ?", money, name);
    }

    /**
     * 查询钱
     * @param name 查询账户
     * @return 余额
     */
    @Override
    public double queryMoney(String name) {
        Double aDouble = this.getJdbcTemplate().queryForObject("SELECT money FROM t_account WHERE name = ?", new AccountMapper(), name);

        return aDouble;
    }

    /**
     * 查询映射器
     */
    class AccountMapper implements RowMapper<Double>{
        @Override
        public Double mapRow(ResultSet rs, int rowNum) throws SQLException {
           return rs.getDouble("money");
        }
    }
}
```

业务层
- . 通过使用transactionTemplate.execute(new TransactionCallbackWithoutResult() {})，内部类方法体内的代码进行事务管理。

```java
public interface AccountService {
    void transfer(String in, String out, double money);
}

/**
 * @author liisyu
 * @date 2019/3/1
 */
public class AccountServiceImpl implements AccountService {
//    该类是Spring用来简化事务管理的模板类
    private TransactionTemplate transactionTemplate;
    private AccountDao accountDao;

    public void setTransactionTemplate(TransactionTemplate transactionTemplate) {
        this.transactionTemplate = transactionTemplate;
    }

    public void setAccountDao(AccountDao accountDao) {
        this.accountDao = accountDao;
    }

    /**
     * 用于模拟转账
     * @param out 转出账户
     * @param in 转入账户
     * @param money 转移钱数
     */
    @Override
    public void transfer(String out, String in, double money) {
        transactionTemplate.execute(new TransactionCallbackWithoutResult() {
            @Override
            protected void doInTransactionWithoutResult(TransactionStatus transactionStatus) {
                //查询钱
                double aMoney = accountDao.queryMoney(out);
                //扣钱
                accountDao.updateMoney(out, aMoney - money);

//                System.out.println(1/0);
                //查询钱
                aMoney = accountDao.queryMoney(in);
                //加钱
                accountDao.updateMoney(in, aMoney + money);
            }
        });

    }
}

```
配置文件
1. 配置连接池
2. 配置业务层和持久层
3. 配置事务管理器:DataSourceTransactionManager
4. 配置事务管理器模板:TransactionTemplate

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
           http://www.springframework.org/schema/context
           http://www.springframework.org/schema/context/spring-context.xsd">

    <context:property-placeholder location="classpath:db.properties"/>
    <!--设置C3P0连接池-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${db.driverClassName}"/>
        <property name="jdbcUrl" value="${db.url}"/>
        <property name="user" value="${db.username}"/>
        <property name="password" value="${db.password}"/>
    </bean>

    <!--配置bean-->
    <bean id="accountDao" class="com.liisyu.dao.impl.AccountDaoImpl">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <bean id="accountService" class="com.liisyu.service.impl.AccountServiceImpl">
        <property name="accountDao" ref="accountDao"/>
        <property name="transactionTemplate" ref="transactionTemplate"/>
    </bean>

    <!--配置事务管理器-->
    <bean id="transactionManager"
          class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!--配置事务模板-->
    <bean id="transactionTemplate"
          class="org.springframework.transaction.support.TransactionTemplate">
        <property name="transactionManager" ref="transactionManager"/>
    </bean>
</beans>
```


## 声明式事务管理(重点掌握)

声明式事务管理分为两种方式
1. 基于AspectJ的XML方式（重点掌握）
1. 基于AspectJ的注解方式（重点掌握）

### 基于AspectJ的XML方式实现

