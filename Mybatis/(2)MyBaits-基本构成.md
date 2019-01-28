# MyBatis 基本构成

|组件|说明|
|--|--|
|SqlSessionFactoryBuilder|根据配置信息或者代码生成SqlSessionFactory|
|SqlSessionFactory|依靠工厂来生成SqlSession|
|SqlSession|可以发送Sql去执行返回结果，也可以获取Mapper的接口|
|SQL Mapper|MyBatis新设计组件，由一个Java接口和XML文件（或注解）构成，需要给出对应的SQL和映射规则。负责发送SQL去执行，并返回结果|

关系图：

![MyBaits-core.png](https://raw.githubusercontent.com/FameLsy/Images/master/MyBatis/MyBaits-core.png)

说明:
1. 通过SqlSessionFactoryBuilder来加载配置信息，获取定制的SqlSessionFactory
2. SqlSessionFactory获取SqlSession
3. SqlSession相当于一个连接，用于连接数据库,并且作用是通过自身或者获取映射器进行Sql操作
4. Mapper映射器作用就是进行数据库的操作


#  SqlSessionFactoryBuilder 

SqlSessionFactoryBuilder 作用：  
从 XML 配置文件或一个预先定制的 Configuration 的实例构建出 SqlSessionFactory 的实例

# MyBaits的核心 SqlSessionFactory

SqlSessionFactory：
1. 是一个工厂接口
2. 任务是创建SqlSession

SqlSessionFactory的创建
1. XML配置
2. 代码方式

SqlSessionFactory实现类
1. DefaultSqlSessionFactory
2. SqlSessionManager(较少用)

此外，因为SqlSession类似于Connection，而SqlSessionFactory相当于连接池，为了保证资源不被很快耗尽，所以SqlSessionFactory是单例模式。

## XML方式构建SqlSessionFactory（推荐）

构建方式：
1. 创建一个XML输入流
2. MyBatis 包含一个名叫 Resources 的工具类，它包含一些实用方法，可使从 classpath 或其他位置加载资源文件更加容易
3. 使用SqlSessionFactroyBuilder读取XML信息来创建SqlSessionFactory对象
4. 其中，XML的配置信息会解析到Configuration类对象里面，SqlSessionFactroyBuilder就是通过读取改对象创建的SessionFactory



```java
String resource = "mybatis-config.xml";
Inputstream inpoutStream = Resources.getResourceAsStream(resource);
SqlSessionFactory sqlSessionFactory = null;
sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
```

## 代码方式构建SqlSessionFactory

构建方式

1. 代码中初始化了一个数据库连接池
2. 定义连JDBC的数据库事务管理方式
3. 使用数据库连接池和数据库事务管理方式创建了一个数据库运行环境,并命名为development
4. 创建了一个Configuration类对象，将数据库运行环境注册给它
5. 注册了一个role的别名
6. 加入了一个映射器
7. 通过SqlSessionFactoryBuilder读取Configuration对象创建SqlSessionFactory

```java
// package com.masorl.code2_3;
public class MyBaitsDemo {
    public static void main(String[] args) {
//        构建数据库环境
        PooledDataSource dataSource = new PooledDataSource();
        dataSource.setDriver("com.mysql.jdbc.Driver");
        dataSource.setUrl("jdbc:mysql：//localhost:3306/mybatis");
        dataSource.setUsername("root");
        dataSource.setPassword("123456");
//        构建数据库事务方式
        TransactionFactory transactionFactory = new JdbcTransactionFactory();
//        构建数据库运行环境
        Environment environment = new Environment("development",transactionFactory,dataSource);
//        构建Configuration对象
        Configuration configuration = new Configuration(environment);
//        注册一个Mybatis上下文别名
        configuration.getTypeAliasRegistry().registerAlias("role",Role.class);
//        加入映射器
        configuration.addMapper(RoleMapper.class);
//        使用SqlSessionFactoryBuilder构建SqlSessionFactory
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(configuration);
    }
}
```

在本质上，就是通过Java代码来实现XML中的相关配置

# SqlSession，前台服务员

SqlSession:
1. 类似于一个JDBC的Conneciton
2. 是一个接口
3. 需要保证每次用完都会关闭Sqlsession
4. 线程不安全


SqlSeiion 实现类
1. DefaultSqlSession
2. SlqSessionManger

## 标准SqlSession用法

以下是伪代码
```java
// package com.masorl.code2_4;
public class SqlSessionDemo {
    public static void main(String[] args) {
//        伪代码
        SqlSession sqlSession = null;
        try{
            sqlSession = sqlSessionFactroy.openSession();
//          some code
            sqlSession.commit();
        }catch (Exception e){
            System.out.println(e.getMessage());
            sqlSession.rollback();
        }finally {
            if (sqlSession != null){
                sqlSession.close();
            }       
        }
    }
}
```

## SqlSession用途
1. 直接通过命名信息去执行SQL返回结果（iBatis版本遗留，不推荐）
2.  获取映射器，让映射器通过命名空间和方法名称找到对应的SQL，发送给数据库执行返回结果(推荐)

用途1解释： SqlSession 完全包含了面向数据库执行 SQL 命令所需的所有方法可以通过 SqlSession 实例来直接执行已映射的 SQL 语句
 ```java
 SqlSession session = sqlSessionFactory.openSession();
try {
  Blog blog = (Blog) session.selectOne("org.mybatis.example.BlogMapper.selectBlog", 101);
} finally {
  session.close();
}
```
用途2解释：通过SqlSession获取映射器来执行SQL
```java
SqlSession session = sqlSessionFactory.openSession();
try {
  BlogMapper mapper = session.getMapper(BlogMapper.class);
  Blog blog = mapper.selectBlog(101);
} finally {
  session.close();
}
```

用途1，2对比之下，2可以执行更清晰和类型安全的代码，而且还不用担心易错的字符串字面值以及强制类型转换。

# Configuration,配置信息

Configuration: 
1. 全限定名：rog.apache.ibatis.seeion.Configuration
2. 在Mybatis中以对象的形式存在于整个应用的生命周期,以便重复读取和云用
3. 保存配置信息
4. 单例


# Mapper, 映射器

单独一章

