---
title: spring框架事务管理的分类
tgas: 
-  spring框架事务管理的分类
categories: 
- Spring 
---


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

在Spring的编程式事务管理的基础下，做如下改动
1. 不再使用TransactionTemplate类来管理
2. 利用AspectJ来切入事务通知达到事务管理的目的

具体做法如下
1. 业务层不再需要TransactionTemplate类，只是一个普通的类

```java
/**
 * @author liisyu
 * @date 2019/3/1
 */
public class AccountServiceImpl2 implements AccountService {
//    该类是Spring用来简化事务管理的模板类
    private AccountDao accountDao;

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
}
```

2. 配置文件添加事务通知和AOP
```xml
    <!--事务通知-->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <!--设置事务管理信息-->
        <tx:attributes>
            <!--查询使用read-only-->
            <!--<tx:method name="query" read-only="true"/>-->
            <!--增删改使用propagation="REQUIRED"事务传播行为,隔离级别选择DEFAULT-->
            <tx:method name="transfer*" propagation="REQUIRED" isolation="DEFAULT"/>
        </tx:attributes>
    </tx:advice>

<!--基于AspectJ + XML方式实现声明式事务-->
<aop:config>
    <aop:advisor advice-ref="txAdvice" pointcut="execution(* *..*.*ServiceImpl2.*(..))"/>
</aop:config>
```

### 基于AspectJ的注解方式实现（重点掌握）

对于注解模式，在Spring的编程式事务管理的基础下，做如下改动
1. 开启事务注解

```xml
   <!--配置事务管理器-->
    <bean id="transactionManager"
          class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!--开启事务注解-->
    <tx:annotation-driven transaction-manager="transactionManager"/>
```

2. 使用@Transactional注释  
    使用在类上，类中所有方法开启事务
    使用在方法上，单一方法开始事务