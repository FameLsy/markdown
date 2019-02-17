---
title: Mybatis实例
date: 2019-02-16 00:00:20
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