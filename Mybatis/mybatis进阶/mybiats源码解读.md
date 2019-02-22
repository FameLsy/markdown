---
title: mybaits进阶 
date: 2019-02-22 00:00:01
tags: 
- maven 
categories:
- maven
---


![tomcat](https://raw.githubusercontent.com/FameLsy/Images/master/hexo/tomcat.png)


# DefaultSqlSessionFactory创建过程

想要创建DefaultSqlSessionFactor，那么肯定要用到如下方法
```java
SqlSessionFactoryBuilder().build（）
```


## SqlSessionFactoryBuilder类

可以看出,该类只有一个无参构造函数和重写的build方法.

![SqlSessionFactoryBuilder类](https://raw.githubusercontent.com/FameLsy/Images/master/maven/SqlSessionFactoryBuilder.png)


主要的build方法是有三个参数,而其他的build方法体中调用三参数的方法(有两个主要方法，分区别在于传入Reader和InputStream)
```java
 public SqlSessionFactory build(InputStream inputStream, String environment, Properties properties) {
        SqlSessionFactory var5;
        try {
            XMLConfigBuilder parser = new XMLConfigBuilder(inputStream, environment, properties);
            var5 = this.build(parser.parse());
        } catch (Exception var14) {
            throw ExceptionFactory.wrapException("Error building SqlSession.", var14);
        } finally {
            ErrorContext.instance().reset();

            try {
                inputStream.close();
            } catch (IOException var13) {
                ;
            }

        }

        return var5;
    }
```

## 第一句
```
 XMLConfigBuilder parser = new XMLConfigBuilder(inputStream, environment, properties);
 ```

主要是一个XMLconfigBuilder对象，进入
```
public class XMLConfigBuilder extends BaseBuilder {
    private boolean parsed;
    private final XPathParser parser;
    private String environment;
    private final ReflectorFactory localReflectorFactory;

```
定义了XPathParser，推断出，这是一个xml解析器，也就是说这个类是一个XML解析类，解析MyBaits的配置文件  

## 第二句


```java
var5 = this.build(parser.parse());
```
可以看到它执行了XMLConfigBuilder的parse()方法，该方法具体定义如下
```java
public Configuration parse() {
        if (this.parsed) {
            throw new BuilderException("Each XMLConfigBuilder can only be used once.");
        } else {
            this.parsed = true;
            this.parseConfiguration(this.parser.evalNode("/configuration"));
            return this.configuration;
        }
    }

```
parsed是一个布尔类型，用来保证XMLConfigBuilder的唯一性。  

该方法返回配置对象Configuration,而这个方法返回有传递给build()方法

```java
 public SqlSessionFactory build(Configuration config) {
        return new DefaultSqlSessionFactory(config);
    }
```

该build()方法返回DefaultSqlSessionFactory。这个应该很熟悉了，就是MyBaits的默认SqlSessionFactory

## 第三句
可以看到，这里关闭了InputStream流，因此，我们对于Mybaits的读取流，不需要关闭它
```java
finally {
            ErrorContext.instance().reset();
            try {
                inputStream.close();
            } catch (IOException var13) {
                ;
            }
        }

```

至此，一个加载了配置文件信息的SqlSessionFactory完成了

# SqlSession的创建过程

获取SqlSession,需要调用如下方法
```
aSqlSessionFactory.openSession()方法
```

## DefaultSqlSessionFactory类

先来看下DefaultSqlSessionFactory类

![DefaultSqlSessionFactory](https://raw.githubusercontent.com/FameLsy/Images/master/MyBatis/DefaultSqlSessionFactory.png)

opensession方法内部，都调用了一个openSessionFromDataSource()方法
```java
   public SqlSession openSession() {
        return this.openSessionFromDataSource(this.configuration.getDefaultExecutorType(), (TransactionIsolationLevel)null, false);
    }

    //this.configuration.getDefaultExecutorType(),返回了一个defaultExecutorType(翻译以下就是默认执行器对象)
    public ExecutorType getDefaultExecutorType() {
        return this.defaultExecutorType;
    }
```
## openSessionFromDataSource()
```
private SqlSession openSessionFromDataSource(ExecutorType execType, TransactionIsolationLevel level, boolean autoCommit) {
        Transaction tx = null;

        DefaultSqlSession var8;
        try {
            //getEnvironment()正是获取Mybatis< environment>标签内容（该标签存储数据库连接属性）
            Environment environment = this.configuration.getEnvironment();

            //可以从方法名知道，该方法是从环境获取事务工厂（事务也是配置在< environment>）
            TransactionFactory transactionFactory = this.getTransactionFactoryFromEnvironment(environment);

            //从事务工厂创建了新的事务对象，从环境中获取数据库配置信息
            tx = transactionFactory.newTransaction(environment.getDataSource(), level, autoCommit);

            //创建一个执行器
            Executor executor = this.configuration.newExecutor(tx, execType);

            //传入配置，执行器，自动提交标识(布尔值)
            var8 = new DefaultSqlSession(this.configuration, executor, autoCommit);
        } catch (Exception var12) {
            this.closeTransaction(tx);
            throw ExceptionFactory.wrapException("Error opening session.  Cause: " + var12, var12);
        } finally {
            ErrorContext.instance().reset();
        }

        return var8;
    }
```

就这样，获取了DefaultSqlSession



# SqlSession是如何执行Sql语句和传入值的

```
public class DefaultSqlSession implements SqlSession {
    //放置配置信息
    private final Configuration configuration;
    //执行器
    private final Executor executor;
    //事务自动提交
    private final boolean autoCommit;
    private boolean dirty;
    private List<Cursor<?>> cursorList;
```

## aSqlSession.insert()方法
1. getRole为Mybaits的id
2. aRole用于传入数据

```
//插入语句示例
aSqlSession.insert("getRole", aRole);

//插入语句源码
public int insert(String statement, Object parameter) {
    //在这里调用了一个自定义的update方法
    return this.update(statement, parameter);
}

```
##  update 方法

带着疑问
1. dirty是用来干嘛的: false，回滚，true,提交

```
    public int update(String statement, Object parameter) {
        int var4;
        try {
            //dirty,SqlSession的属性，创建时为false
            this.dirty = true;

            //  从配置对象获取配置文件中与当前id对应的SQL语句，MappedStatement中存放了Sql语句
            MappedStatement ms = this.configuration.getMappedStatement(statement);

            //#1:
            //warCollection()会根据parameter返回其类型
            var4 = this.executor.update(ms, this.wrapCollection(parameter));
        } catch (Exception var8) {
            throw ExceptionFactory.wrapException("Error updating database.  Cause: " + var8, var8);
        } finally {
            ErrorContext.instance().reset();
        }

        return var4;
    }


    //——>#1 
    private Object wrapCollection(Object object) {
        DefaultSqlSession.StrictMap map;
        //判断是否是集合
        if (object instanceof Collection) {
            map = new DefaultSqlSession.StrictMap();
            map.put("collection", object);
            if (object instanceof List) {
                map.put("list", object);
            }

            return map;
        } 
        //判断是否map
        else if (object != null && object.getClass().isArray()) {
            map = new DefaultSqlSession.StrictMap();
            map.put("array", object);
            return map;
        } 
        //否则原样返回
        else {
            return object;
        }
    }
```