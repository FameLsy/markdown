---
title: Spring-JDBC
date: 2018-02-27 00:00:03
tgas: 
- Spring-JDBC
categories: 
- Spring 
---

知识点:
1. JdbcTemplate模板类的使用
2. 默认数据库连接池DriverManagerDataSource
3. Spring第三方连接池的使用
4. JdbcDaoSupport,提供简化操作

# JDBC-MAVEN
导入
1. mysql驱动程序包
2. spring对jdbc的支持

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>${mysql.connector.version}</version>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>${spring.version}</version>
</dependency>
```

# Spring提供的JDBC支持类

JdbcTemplate类：模板类
DriverManagerDataSource: Spring的内置连接池

## 手动创建:
```java
/**
 * 简单的Spring-JDBC测试
 * @author liisyu
 * @date 2019/2/28
 */
public class TestJdbcTemplate {
    @Test
    public void test() {
        // 创建连接池，先使用Spring框架内置的连接池
        DriverManagerDataSource dataSource = new DriverManagerDataSource();
        dataSource.setDriverClassName("com.mysql.jdbc.Driver");
        dataSource.setUrl("jdbc:mysql://localhost:3306/mydb?serverTimezone=GMT%2B8");
        dataSource.setUsername("masorl");
        dataSource.setPassword("314512321lsy");
        // 创建模板类
        JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);//我们自己编写原始的JDBC代码，可能需要9步
        // 完成数据的添加
        jdbcTemplate.update("insert into account values ('liisyu',?,?)", "测试",10000);

    }
}
```

## 使用spring来管理
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
           http://www.springframework.org/schema/context
           http://www.springframework.org/schema/context/spring-context.xsd">

    <!--导入properties文件-->
    <context:property-placeholder location="classpath:db.properties"/>
    <!--设置连接池-->
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="${db.driverClassName}"/>
        <property name="url" value="${db.url}"/>
        <property name="username" value="${db.username}"/>
        <property name="password" value="${db.password}"/>
    </bean>
    <!--设置Spring JdbcTemplate-->
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>
</beans>
```

## 基于JdbcTemplate的CURD

代码
```java
/**
 * 基于JdbcTemplate的CURD
 * @author liisyu
 * @date 2019/2/28
 */
@RunWith(SpringJUnit4ClassRunner.class )
@ContextConfiguration("classpath:jdbctest/jdbc.xml")
public class TestJdbcTemplate2 {
    @Resource(name="jdbcTemplate")
    private JdbcTemplate jdbcTemplate;
    //插入
    @Test
    public void test() {
        jdbcTemplate.update("insert into account values ('liisyu3',?,?)", "测试2",10000);
    }

    //更新
    @Test
    public void test2(){
        jdbcTemplate.update("update account set boy=?,descrption =? where number = ?", "思雨",10000d,10000);
    }

    //删除
    @Test
    public void test3(){
        jdbcTemplate.update("delete from account where number = ?", 10000);
    }

    /**
     * 查询单个
     * 需要导入一个映射器
     */
    @Test
    public void test4(){
        Account account = jdbcTemplate.queryForObject("select * from account where number = ?", new BeanMapper(), 10000);
        System.out.println(account.toString());
    }

    /**
     * 查询所有
     * 需要导入一个映射器
     */
    @Test
    public void demo5(){
        List<Account> list = jdbcTemplate.query("select * from account", new BeanMapper());
        for (Account account : list) {
            System.out.println(account.toString());
        }
    }

}
```

映射器
1. 实现接口RowMapper
2. 重写方法mapRow
3. 主要是将数据库类型转换成Java类型

```java
import org.springframework.jdbc.core.RowMapper;
/**
 * 映射器
 * @author liisyu
 * @date 2019/3/1
 */
public class BeanMapper implements RowMapper<Account> {
    @Override
    public Account mapRow(ResultSet rs, int rowNum) throws SQLException {
        Account account = new Account();
        account.setBoy(rs.getString("boy"));
        account.setDescrption(rs.getString("descrption"));
        account.setNumber(rs.getInt("number"));
        return account;
    }
}
```

测试类
```java
@RunWith(SpringJUnit4ClassRunner.class )
@ContextConfiguration("classpath:jdbctest/jdbc.xml")
public class TestJdbcTemplate {
    @Resource(name="jdbcTemplate")
    private JdbcTemplate jdbcTemplate;

    @Test
    public void test2() {
        jdbcTemplate.update("insert into account values ('liisyu2',?,?)", "测试2",10000);
    }

}
```

# Spring管理第三方连接池

## 管理DBCP

pom:
```xml
<!--dbcp连接池依赖-->
<dependency>
    <groupId>commons-dbcp</groupId>
    <artifactId>commons-dbcp</artifactId>
    <version>${dbcp.version}</version>
</dependency>
```

配置
```xml
<!--设置DBCP连接池-->
<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource">
    <property name="driverClassName" value="${db.driverClassName}"/>
    <property name="url" value="${db.url}"/>
    <property name="username" value="${db.username}"/>
    <property name="password" value="${db.password}"/>
</bean>
```

## 管理C3P0连接池

pom
```xml
<dependency>
    <groupId>com.mchange</groupId>
    <artifactId>c3p0</artifactId>
    <version>${c3p0.version}</version>
</dependency>
```

配置
```xml
<!--设置C3P0连接池-->
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="driverClass" value="${db.driverClassName}"/>
    <property name="jdbcUrl" value="${db.url}"/>
    <property name="user" value="${db.username}"/>
    <property name="password" value="${db.password}"/>
</bean>
```

# Spring DAO开发（JdbcDAoSupport）

使用Spring管理JdbcTemplate类后，我们需要写如下一步导入jdbc
```java
@Resource(name="jdbcTemplate")
private JdbcTemplate jdbcTemplate;
```
并且在xml配置JdbcTemplate,并传入dataSource
```xml
<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
    <property name="dataSource" ref="dataSource"/>
</bean>
```

Spring提供了JdbcDAoSupport，帮我们省去这一步，只需要继承该类
```java
/**
 * @author liisyu
 * @date 2019/3/1
 */
public class DemoDao extends JdbcDaoSupport {
    /**
     * 继承后，使用this.getJdbcTemplate()获取JdbcTemplate
     */
    public void test(){
        this.getJdbcTemplate().update("insert into account values ('liisyu3',?,?)", "测试2",100002);
    }
}
```
并且配置文件，需要为该类注入DataSource
```xml
    <bean id="demoDao" class="com.liisyu.Jdbctest.DemoDao">
        <property name="dataSource" ref="dataSource"/>
    </bean>
```

实际上本质是JdbcDaoSupport帮我们完成了这件事
```java
public abstract class JdbcDaoSupport extends DaoSupport {

	@Nullable
	private JdbcTemplate jdbcTemplate;

    	/**
	 * Set the JDBC DataSource to be used by this DAO.
	 */
	public final void setDataSource(DataSource dataSource) {
		if (this.jdbcTemplate == null || dataSource != this.jdbcTemplate.getDataSource()) {
			this.jdbcTemplate = createJdbcTemplate(dataSource);
			initTemplateConfig();
		}
	}

    	@Nullable
	public final JdbcTemplate getJdbcTemplate() {
	  return this.jdbcTemplate;
	}

...
```