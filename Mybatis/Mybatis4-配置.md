---
title: Mybatis配置
tags: 
- Mybatis配置
categories: 
- Mybaits
---
# 配置文件属性
configuration 配置  
|配置|说明|
|--|--|
|properties| 属性,主要是定义一些字符常量等属性共Mybatis上下文使用|
|settings |设置|
|typeAliases |类型别名,主要时用来减少完全限定名的冗余|
|typeHandlers |类型处理器|
|objectFactory |对象工厂|
|plugins |插件|
|environments |环境|
|databaseIdProvider |数据库厂商标识|
mappers |映射器|

# properties 属性

properties的三种配置方式：
1. property 子元素
2. properties配置文件
3. 程序参数传递

## property 子元素配置方式

配置文件
```xml
<properties resource="org/mybatis/example/config.properties">
  <property name="username" value="dev_user"/>
  <property name="password" value="F2Fa3!33TYyg"/>
</properties>
```
## properties配置文件

.properties文件
```
username=dev_user
password=F2Fa3!33TYyg
```
在XML配置中引入properties文件
```xml
<properties resource="../xxx.properties">
```

##  程序参数传递

即通过参数的形式，将properties和xml配置作为参数传入SqlSessionFactoryBuilder构造方法中，以此直接构造一个SqlSessionFactory对象.
```java
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, props);

// ... or ...

SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, environment, props);
```

实际应用：
properties需要进行节码后再将properties属性传入

```java
// 伪代码
package com.masorl.code3_5;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.Reader;
import java.util.Properties;

public class MyBatisDemo {
    public static void main(String[] args) {
//        配置文件输入流
        InputStream configurationStream = null;
        Reader configurationReader = null;
//        properties文件输入流
        InputStream propertiesStream = null;
        Reader propertiesReader = null;
//        properties文件对象
        Properties properties = null;

        try {
//            读入配置文件
            configurationStream = Resources.getResourceAsStream("mybatis-config.xml");
            configurationReader = new InputStreamReader(configurationStream);
//          读入属性文件
            propertiesStream = Resources.getResourceAsStream("jdbc.prperties");
            propertiesReader = new InputStreamReader(propertiesStream);
            properties = new Properties();
            properties.load(propertiesReader);

//            解密,假设解密方法为decode();
//            properties.setProperty("username",
//                    decode(properties.get("username")));
//            properties.setProperty("password",
//                    decode(properties.get("password")));
        }catch (Exception e){

        }
//      将配置文件和properties作为参数传入SqlSessionFactoryBuilder.build()方法
        synchronized (CLASS_LOCK){
            if (sqlSessionFactory = null){
                sqlSessionFactory = new SqlSessionFactoryBuilder().build(configurationReader, properties);
            }
        }


    }
}
```

## 属性的使用
通过${name}的方式来整个配置文件中进行动态配置属性
```xml
<dataSource type="POOLED">
  <property name="driver" value="${driver}"/>
  <property name="url" value="${url}"/>
  <property name="username" value="${username}"/>
  <property name="password" value="${password}"/>
</dataSource>
```

从MyBatis 3.4.2开始，你可以为占位符指定一个默认值(默认这个特性是关闭的)
```xml
<!-- 开启特效 -->
<properties resource="org/mybatis/example/config.properties">
  <!-- ... -->
  <property name="org.apache.ibatis.parsing.PropertyParser.enable-default-value" value="true"/> <!-- Enable this feature -->
</properties>

<!-- 使用该特性 -->
<dataSource type="POOLED">
  <!--username没有定义时会用ut_user代替 -->
  <property name="username" value="${username:ut_user}"/> 
</dataSource>
```
## 优先级
properties 属性的读取顺序为
1. < property >元素体内的属性先被读取（所以优先级最低，会被覆盖）
2. 再读取< properties >元素体中resource引入的.properties文件
3. 最后读取方法参数引入的属性(所以优先级最高)

因此，建议首选properties文件引入方式，不要混淆使用

# Setting 属性

Setting:
1. 最复杂的配置，可不配置也可使用Mybatis
2. 会改变Mybaits运行时的行为

setting属性解释：  
[具体看官网](http://www.mybatis.org/mybatis-3/zh/configuration.html#properties)

一个完整的setting配置
```xml
<settings>
  <setting name="cacheEnabled" value="true"/>
  <setting name="lazyLoadingEnabled" value="true"/>
  <setting name="multipleResultSetsEnabled" value="true"/>
  <setting name="useColumnLabel" value="true"/>
  <setting name="useGeneratedKeys" value="false"/>
  <setting name="autoMappingBehavior" value="PARTIAL"/>
  <setting name="autoMappingUnknownColumnBehavior" value="WARNING"/>
  <setting name="defaultExecutorType" value="SIMPLE"/>
  <setting name="defaultStatementTimeout" value="25"/>
  <setting name="defaultFetchSize" value="100"/>
  <setting name="safeRowBoundsEnabled" value="false"/>
  <setting name="mapUnderscoreToCamelCase" value="false"/>
  <setting name="localCacheScope" value="SESSION"/>
  <setting name="jdbcTypeForNull" value="OTHER"/>
  <setting name="lazyLoadTriggerMethods" value="equals,clone,hashCode,toString"/>
</settings>
```


# typeAliases属性(别名)

作用： 为java类型设置一个短的名称，它只和 XML 配置有关，存在的意义仅在于用来减少类完全限定名的冗余

这样，就可以使用Author来代表其整个路径了。

Mybatis定义了如下别名:  
[具体看官网](http://www.mybatis.org/mybatis-3/zh/configuration.html#properties)

## 自定义别名

### XML方式
```xml
<typeAliases>
<!-- Author和Blog在其他敌方使用，就相当于后面的全称 -->
  <typeAlias alias="Author" type="com.masorl.blog.Author"/>
  <typeAlias alias="Blog" type="com.masorl..blog.Blog"/>
</typeAliases>
```

### 自定义别名，扫描注解方式

应用场景，当POJO对象过多时，一个个定义别名未免太过于繁琐

MyBatis可以通过扫描包的形式定义别名
```xml
<typeAliases>
  <package name="com.masorl.blog"/>
</typeAliases>
```
那么他将会扫描com.masorl.blog下的所有Java bean  

在没有注解的情况下，会使用bean的首字母小写作为别名,如com.masorl.blog.Author的别名就是"author"

也可以通过@Alias注解方式命名
```java
@Alias("author")
public class Author(){...}
```

# typeHandlers类型处理器

无论是 MyBatis 在预处理语句（PreparedStatement）中设置一个参数时，还是从结果集中取出一个值时， 都会用类型处理器将获取的值以合适的方式转换成 Java 类型。

作用：
1. 将javaType转换成jdbcType
2. 将jdbcType转换成javaType

## 加载

指定类型处理器
```xml
<!-- mybatis-config.xml -->
<typeHandlers>
  <typeHandler handler="org.mybatis.example.ExampleTypeHandler"/>
</typeHandlers>
```
查找类型处理器
```xml
<!-- mybatis-config.xml -->
<typeHandlers>
  <package name="org.mybatis.example"/>
</typeHandlers>
```

系统定义的类型处理器  
[具体看官网](http://www.mybatis.org/mybatis-3/zh/configuration.html#properties)

源码查看在org.apache.ibatis.type包下

## 自定义类型处理器

TypeHandler接口
1. setParameter方法用于对PreparedStatement参数的设置
2. getResult: 可以通过下标，列名，或者存储过程（CallableStatement）来获取结果
```java
public interface TypeHandler<T> {
    void setParameter(PreparedStatement var1, int var2, T var3, JdbcType var4) throws SQLException;

    T getResult(ResultSet var1, String var2) throws SQLException;

    T getResult(ResultSet var1, int var2) throws SQLException;

    T getResult(CallableStatement var1, int var2) throws SQLException;
}
```

自定义一个类型处理器
1. 实现TypeHandler接口
2. 继承BaseTypeHandler类

如下定义的类型处理器将会覆盖已经存在的处理 Java 的 String 类型属性和 VARCHAR 参数及结果的类型处理器
```java
// ExampleTypeHandler.java
@MappedJdbcTypes(JdbcType.VARCHAR)
public class ExampleTypeHandler extends BaseTypeHandler<String> {

  @Override
  public void setNonNullParameter(PreparedStatement ps, int i, String parameter, JdbcType jdbcType) throws SQLException {
    ps.setString(i, parameter);
  }

  @Override
  public String getNullableResult(ResultSet rs, String columnName) throws SQLException {
    return rs.getString(columnName);
  }

  @Override
  public String getNullableResult(ResultSet rs, int columnIndex) throws SQLException {
    return rs.getString(columnIndex);
  }

  @Override
  public String getNullableResult(CallableStatement cs, int columnIndex) throws SQLException {
    return cs.getString(columnIndex);
  }
}
```

类型处理器是如何确定java类型的
1. 通过泛型处理器泛型（本例就是这种方式确定Java类型为String)
2. 在类型处理器的配置元素（typeHandler element）上增加一个 javaType 属性（比如：javaType="String"）；
3. 在类型处理器的类上（TypeHandler class）增加一个 @MappedTypes 注解来指定与其关联的 Java 类型列表(比如：@MappedTypes{String.class})。 如果在 javaType 属性中也同时指定，则注解方式将被忽略。

类型处理器是如何确定jdbc类型的
1. 在类型处理器的类上（TypeHandler class）增加一个 @MappedJdbcTypes 注解来指定与其关联的 JDBC 类型列表。 如果在 jdbcType 属性中也同时指定，则注解方式将被忽略。(本例方式)
2. 在类型处理器的配置元素上增加一个 jdbcType 属性（比如：jdbcType="VARCHAR"）；

## 自定义泛型处理器
```java
//GenericTypeHandler.java
public class GenericTypeHandler<E extends MyObject> extends BaseTypeHandler<E> {

  private Class<E> type;

  public GenericTypeHandler(Class<E> type) {
    if (type == null) throw new IllegalArgumentException("Type argument cannot be null");
    this.type = type;
  }
  ...
  
  ```
  EnumTypeHandler 和 EnumOrdinalTypeHandler 都是泛型类型处理器（generic TypeHandlers）
## 特殊的TypeHandler,枚举类型
若想映射枚举类型 Enum，则从以下TypeHandler中选择
1. EnumTypeHandler(使用字符串名称作为参数传递)  
2. EnumOrdinalTypeHandler(使用整数下标作为参数传递，默认枚举处理器) 

# 在ResultMap中使用TypeHandler

在ResultMap中使用TypeHandler
1. java类型可以从结果类型中获得
2. Jdbc类型未知
3. 因此Mybatis使用javaType=[ TheJavaType ], jdbcType=null的组合来选择一个TypeHandler
4. 非显示设置的话，TypeHandler在ResultMap中无效
5. 希望在ResultMap中使用TypeHandler，那么设置@MappedJdbcTypes注解的includeNullJdbcType=true即可
6. 当只有一个TypeHandler，就算没有includeNullJdbcType=true，它也会是ResultMap使用Java类型时的默认。


# objectFactory对象工厂

objectFactory对象工厂:
1. MyBatis 每次创建结果对象的新实例时，它都会使用一个对象工厂（ObjectFactory）实例来完成。   
2. 默认的对象工厂（org.apache.ibatis.reflection.factory.DefaultObjectFactory）
3. 默认的对象工厂需要做的仅仅是实例化目标类，要么通过默认构造方法，要么在参数映射存在的时候通过参数构造方法来实例化

## 自定义工厂对象

ObjectFactory接口：
1. 两个创建用的方法
2. 一个setProperties方法（xml文件中的元素体定义的属性会被传递给 setProperties 方法）
3. 一个判断方法

```java
public interface ObjectFactory {
    void setProperties(Properties var1);

    <T> T create(Class<T> var1);//处理默认构造函数

    <T> T create(Class<T> var1, List<Class<?>> var2, List<Object> var3);//处理带参数的构造函数

    <T> boolean isCollection(Class<T> var1);
}
```

```xml
<!-- mybatis-config.xml -->
<objectFactory type="org.mybatis.example.ExampleObjectFactory">
  <property name="someProperty" value="100"/>
</objectFactory>
```
自定义：  
继承DefaultObjectFactory或实现ObjectFactory接口

```java
// ExampleObjectFactory.java
public class ExampleObjectFactory extends DefaultObjectFactory {
  public Object create(Class type) {
    return super.create(type);
  }
  public Object create(Class type, List<Class> constructorArgTypes, List<Object> constructorArgs) {
    return super.create(type, constructorArgTypes, constructorArgs);
  }
  public void setProperties(Properties properties) {
    super.setProperties(properties);
  }
  public <T> boolean isCollection(Class<T> type) {
    return Collection.class.isAssignableFrom(type);
  }}
  ```
  配置文件引入自定义工厂对象
```xml
  <!-- mybatis-config.xml -->
<objectFactory type="org.mybatis.example.ExampleObjectFactory">
  <property name="someProperty" value="100"/>
</objectFactory>
```

# plugins 插件
插件引用
```xml
<!-- mybatis-config.xml -->
<plugins>
  <plugin interceptor="org.mybatis.example.ExamplePlugin">
    <property name="someProperty" value="100"/>
  </plugin>
</plugins>
```
过于复杂，单独开出一章

# environments 环境

environments环境可注册多个数据源(dataSource)，每一个数据源可分为两大部分
1. 数据库源的配置
2. 数据库事务的配置

例：
```xml
<environments default="development">
  <environment id="development">
    <transactionManager type="JDBC">
      <property name="autoCommit"  value="false"/>
    </transactionManager>
    <dataSource type="POOLED">
      <property name="driver" value="${driver}"/>
      <property name="url" value="${url}"/>
      <property name="username" value="${username}"/>
      <property name="password" value="${password}"/>
    </dataSource>
  </environment>
</environments>
```

## < environments >

default属性：表示在默认情况下，使用哪个数据源配置

## < environment >

一个数据源的配置开始

id属性：表示这个数据源的唯一标识，以便Mybatis上下文使用



## < transactionManager >
数据库事务配置

type属性：可使用三种方式进行配置
1. JDBC: 采用JDBC方式管理事务
2. MANAGED,采用容器方式管理事务，JNDI常用
3. 自定义

property 元素：配置数据事务属性，例中定义了autoCommit=false,不自动提交

>注：  
>如果你正在使用 Spring + MyBatis，则没有必要配置事务管理器， 因为 Spring 模块会使用自带的管理器来覆盖前面的配置。

## < dataSource>

数据库连接信息配置

tpye属性:提供对数据库连接方式的配置
1. UNPOOLED，非数据库连接池配置(由org.apache.ibatis.datasource.unpooled.UnpooledDataSource实现)
2. POOLED,数据库连接池方式(由org.apache.ibatis.datasource.pooled.PooledDataSource实现)
3. JNDI,JDNI数据源(由org.apache.ibatis.datasource.jdni.JdniDataSourceFactory来获取数据源)
4. 自定义数据源(需自己实现org.apache.ibatis.datasource.DataSourceFactory接口)【type=xxx.xxx.MyDataSourceFactroy】

property 元素:配置数据库的连接信息

## 数据库事务的控制

MyBaits将数据库事务交给SqlSession去控制，可以通过SqlSession来提交或回滚。

```java
aSqlSession.commit();
aSqlSession.rollback();
```
# databaseIdProvider,数据库厂商标识

作用：MyBatis 可以根据不同的数据库厂商执行不同的语句，这种多厂商的支持是基于映射语句中的 databaseId 属性

```xml
<databaseIdProvider type="DB_VENDOR">
  <property name="SQL Server" value="sqlserver"/>
  <property name="DB2" value="db2"/>        
  <property name="Oracle" value="oracle" />
</databaseIdProvider>
```

# 映射器 

单独一章