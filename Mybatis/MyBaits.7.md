---
title: Mybatis实例
tags: 
- Mybatis实例
categories: 
- 实例
---

[所有程序github地址](https://github.com/FameLsy/MyBatisCode)
# 最简单的Mybaits入门程序

**目录结构**

![目录结构.png](https://raw.githubusercontent.com/FameLsy/Images/master/MyBatis/%E7%9B%AE%E5%BD%95%E7%BB%93%E6%9E%84.png)

**说明**

|文件|说明|
|--|--|
|Chapter2Main|主程序入口，包含main|
|RoleMapper|映射器|
|RoleMapper.xml|映射器配置文件|
|Role|POJO类|
|SqlSessionFactroyUtil|构建SessionFactory,创建SqlSession|
|mybaits-config|MyBaits配置文件|

**POJO类**
```java
package com.learn.chapter2.po;

/**
 * POJO类
 */
public class Role {
    private Long id = 1L;
    private String roleName;
    private String note;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getRoleName() {
        return roleName;
    }

    public void setRoleName(String roleName) {
        this.roleName = roleName;
    }

    public String getNote() {
        return note;
    }

    public void setNote(String note) {
        this.note = note;
    }
}
```

**MyBaits配置文件**
1. 配置了JDBC数据源
2. 配置了映射器
3. 配置了事务管理，并且关闭自动提交

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <typeAliases>
        <typeAlias alias="role" type="com.learn.chapter2.po.Role"/>
    </typeAliases>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC">
                <property name="autoCommit" value="false"/>
            </transactionManager>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mydb?serverTimezone=GMT%2B8"/>
                <property name="username" value="masorl"/>
                <property name="password" value="314512321lsy"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="roleMapper.xml"/>
    </mappers>
</configuration>
```
**映射器**  
3个抽象方法
```
package com.learn.chapter2.mapper;

import com.learn.chapter2.po.Role;

/**
 * 映射器
 */
public interface RoleMapper {
    Role getRole(Long id);
    int deleteRole(Long id);
    int insertRole(Role role);
}

```

**映射器配置**
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.learn.chapter2.mapper.RoleMapper">
    <select id="getRole" parameterType="long" resultType="role">
        SELECT id, role_name as roleName, note FROM t_role WHERE id = # {id}
    </select>

    <insert id="insertRole" parameterType="role">
        INSERT INTO t_role(role_name, note) VALUES (#{roleName}, #{note})
    </insert>

    <delete id="deleteRole" parameterType="long">
        DELETE FROM t_role WHERE id = #{id}
    </delete>
</mapper>
```

**创建SqlSessionFactory**
1. 为了使一个数据库对应一个SessionFacory,所以使用了单例
2. 将构造方法设为私有防止new方式创建多个对象
3. 加锁防止初始化造成对象的不唯一
4. 提供了一个构建SqlSessionFactory对象方法
5. 提供了一个构建SqlSession方法

```java
package com.learn.chapter2.util;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;

/**
 * 构建SqlSessionFactory，并创建SqlSession
 */
public class SqlSessionFactoryUtil {
//    SqlSessionFactory 对象
    private static SqlSessionFactory sqlSessionFactory = null;
//    类线程锁
    private static final Class CLASS_LOCK = SqlSessionFactoryUtil.class;

    /**
     * 私有化构造函数
     */
    private SqlSessionFactoryUtil(){}

    /**
     * 构建SqlSessionFactory
     */
    public static SqlSessionFactory initSqlSessionFactory(){
        String resource = "mybatis-config.xml";
        InputStream inputStream = null;
        try{
            inputStream = Resources.getResourceAsStream(resource);
        }catch (IOException e){
//            Logger.getLogger(SqlSessionFactoryUtil.class.getName()).log(Level.SEVERE, null, e);
        }
        synchronized (CLASS_LOCK){
            if (sqlSessionFactory == null) sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        }
        return sqlSessionFactory;
    }

    /**
     * 打开SqlSession
     */
    public static SqlSession openSqlSession(){
        if (sqlSessionFactory == null) initSqlSessionFactory();
        return sqlSessionFactory.openSession();
    }
}
```

**程序运行**
```java
package com.learn.chapter2.main;


import com.learn.chapter2.mapper.RoleMapper;
import com.learn.chapter2.po.Role;
import com.learn.chapter2.util.SqlSessionFactoryUtil;
import org.apache.ibatis.session.SqlSession;

import java.io.IOException;

/**
 * 运行MyBatis程序入口
 */
public class Chapter2Main {
    public static void main(String[] args) throws IOException {
        SqlSession sqlSession = null;
        try {
            sqlSession = SqlSessionFactoryUtil.openSqlSession();
            RoleMapper roleMapper = sqlSession.getMapper(RoleMapper.class);
            Role role = new Role();
            role.setRoleName("demo test");
            role.setNote("demo note");
            roleMapper.insertRole(role);
            roleMapper.deleteRole(1L);
            sqlSession.commit();
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            if (sqlSession != null){
                sqlSession.close();
            }
        }
    }
}
```

# 属性Properties参数传入
1. build()方法传入配置信息和属性对象
2. 使用aProperties.setProperty()方法来传递参数
```java
public class SqlSessionFactoryUtil {
//    SqlSessionFactory 对象
    private static SqlSessionFactory sqlSessionFactory = null;
//    类线程锁
    private static final Class CLASS_LOCK = SqlSessionFactoryUtil.class;

    /**
     * 私有化构造函数
     */
    private SqlSessionFactoryUtil(){}

    /**
     * 构建SqlSessionFactory
     */
    public static SqlSessionFactory initSqlSessionFactory(){
        //        配置文件输入流
        InputStream configurationStream = null;
        Reader configurationReader = null;
//        properties文件输入流
        InputStream propertiesStream = null;
        Reader propertiesReader = null;
//        properties文件对象
        Properties properties = null;

        String resource = "mybatis-config.xml";
        String jdbcResource = "jdbc.properties";
        try{
//          读入配置文件
            configurationStream = Resources.getResourceAsStream(resource);
            configurationReader = new InputStreamReader(configurationStream);
//            读入属性文件
            propertiesStream = Resources.getResourceAsStream(jdbcResource);
            propertiesReader = new InputStreamReader(propertiesStream);
            properties = new Properties();
            properties.load(propertiesReader);
//          设置属性
            properties.setProperty("username", properties.getProperty("username"));
            properties.setProperty("url", properties.getProperty("url"));
            properties.setProperty("driver", properties.getProperty("driver"));
            properties.setProperty("password", properties.getProperty("password"));

        }catch (IOException e){
//            Logger.getLogger(SqlSessionFactoryUtil.class.getName()).log(Level.SEVERE, null, e);
        }
        synchronized (CLASS_LOCK){
            if (sqlSessionFactory == null) sqlSessionFactory = new SqlSessionFactoryBuilder().build(configurationReader,properties );
        }
        return sqlSessionFactory;
    }

    /**
     * 打开SqlSession
     */
    public static SqlSession openSqlSession(){
        if (sqlSessionFactory == null) initSqlSessionFactory();
        return sqlSessionFactory.openSession();
    }
```

# 自定义TypeHandler

1 在配置文件中注册TypeHandler

```xml
    <typeHandlers>
        <typeHandler handler="com.learn.chapter2.typeHandler.StringTypeHandler" javaType="string" jdbcType="VARCHAR"/>
    </typeHandlers>
```

2 编写自己的TypeHandler  
在这里我定义了一个通过列名获取结果的方法

```java
public class StringTypeHandler extends BaseTypeHandler<String>{
    
    
    @Override
    public String getResult(ResultSet rs, String columnName) throws SQLException {
        System.out.println("自定义的getResult,列名方式获取");
        return rs.getString(columnName);
    }

    @Override
    public void setNonNullParameter(PreparedStatement preparedStatement, int i, String s, JdbcType jdbcType) throws SQLException {
        
    }

    @Override
    public String getNullableResult(ResultSet resultSet, String s) throws SQLException {
        return null;
    }

    @Override
    public String getNullableResult(ResultSet resultSet, int i) throws SQLException {
        return null;
    }

    @Override
    public String getNullableResult(CallableStatement callableStatement, int i) throws SQLException {
        return null;
    }
}
```

3. 映射器  
定义一个通过列名获取Role类型的方法

```
    Role findRole(String roleName);
```
4 配置映射配置文件，使用TypeHandler
1. 配置一个resultMap(返回类型为自定义类型为Role),其中指定了note列必须用我们自定义的处理器
2. role_name列没有指定，但满足自定义的两种类型
3. id类完全不相关
4. select 中设置了resultmap指向我们定义的roleMap

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.learn.chapter2.mapper.RoleMapper">
    <resultMap id="roleMap" type="role">
        <id column="id" property="id" javaType="long" jdbcType="BIGINT"/>
        <result column="role_name" property="roleName" jdbcType="VARCHAR" javaType="string"/>
        <result column="note" property="note" typeHandler="com.learn.chapter2.typeHandler.StringTypeHandler"/>
    </resultMap>

    <select id="findRole" parameterType="string" resultMap="roleMap">
        SELECT id, role_name, note FROM t_role
        WHERE role_name like concat('%', #{rolename} ,'%')
    </select>
</mapper>
```


5. 结果

会运行两次，一次指定的，一次根据类型自己寻找
```
自定义的getResult,列名方式获取
自定义的getResult,列名方式获取
```

# 主键自增
设置 useGeneratedKeys=”true”，然后再把 keyProperty 设置到目标属性上
```xml
<insert id="insertAuthor" useGeneratedKeys="true"
    keyProperty="id">
  insert into Author (username,password,email,bio)
  values (#{username},#{password},#{email},#{bio})
</insert>
```