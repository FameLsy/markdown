---
title: Mybatis映射器
tags: 
- Mybatis映射器
categories: 
- Mybaits
---

映射器： 由Java接口和XML文件（或注解）共同组成

映射器作用：
1. 定义参数类型
2. 描述缓存
3. 描述Sql语句
4. 定义查询结果和POJO的映射关系

映射器的实现：
1. XML方式
2. 代码方式


# 映射器引入

```xml
<!-- 使用相对于类路径的资源引用 -->
<mappers>
  <mapper resource="org/mybatis/builder/AuthorMapper.xml"/>
  <mapper resource="org/mybatis/builder/BlogMapper.xml"/>
  <mapper resource="org/mybatis/builder/PostMapper.xml"/>
</mappers>
```

```xml
<!-- 使用完全限定资源定位符（URL） -->
<mappers>
  <mapper url="file:///var/mappers/AuthorMapper.xml"/>
  <mapper url="file:///var/mappers/BlogMapper.xml"/>
  <mapper url="file:///var/mappers/PostMapper.xml"/>
</mappers>
```

```xml
<!-- 使用映射器接口实现类的完全限定类名 -->
<mappers>
  <mapper class="org.mybatis.builder.AuthorMapper"/>
  <mapper class="org.mybatis.builder.BlogMapper"/>
  <mapper class="org.mybatis.builder.PostMapper"/>
</mappers>
```

```xml
<!-- 将包内的映射器接口实现全部注册为映射器 -->
<mappers>
  <package name="org.mybatis.builder"/>
</mappers>
```

# 映射器相关元素
< mapper >
1. cache – 给定命名空间的缓存配置。
2. cache-ref – 其他命名空间缓存配置的引用。
4. resultMap – 是最复杂也是最强大的元素，用来描述如何从数据库结果集中来加载对象。
5. parameterMap – 已废弃！老式风格的参数映射。内联参数是首选,这个元素可能在将来被移除，这里不会记录。
6. sql – 可被其他语句引用的可重用语句块。
7. insert – 映射插入语句
8. update – 映射更新语句
9. delete – 映射删除语句
10. select – 映射查询语句


## select 元素
例：
```xml
<select id="selectPerson" parameterType="int" resultType="hashmap">
  SELECT * FROM PERSON WHERE ID = #{id}
</select>
```
注意#{id}，表示 MyBatis 创建一个预处理语句参数，相当于
```java
// Similar JDBC code, NOT MyBatis…
String selectPerson = "SELECT * FROM PERSON WHERE ID=?";
PreparedStatement ps = conn.prepareStatement(selectPerson);
ps.setInt(1,id);
```

select 元素具体属性

| **属性**          | **描述**                                                                                                                                                                                                                               |
|--|--|
| id                | 在命名空间中唯一的标识符，可以被用来引用这条语句。                                                                                                                                                                                     |
| parameterType     | 将会传入这条语句的参数类的完全限定名或别名。这个属性是可选的，因为 MyBatis 可以通过 TypeHandler 推断出具体传入语句的参数，默认值为 unset。                                                                                             |
| parameterMap      | 这是引用外部 parameterMap 的已经被废弃的方法。使用内联参数映射和 parameterType 属性。                                                                                                                                                  |
| resultType        | 从这条语句中返回的期望类型的类的完全限定名或别名。注意如果是集合情形，那应该是集合可以包含的类型，而不能是集合本身。使用 resultType 或 resultMap，但不能同时使用。                                                                     |
| resultMap         | 外部 resultMap 的命名引用。结果集的映射是 MyBatis 最强大的特性，对其有一个很好的理解的话，许多复杂映射的情形都能迎刃而解。使用 resultMap 或 resultType，但不能同时使用。                                                               |
| flushCache        | 将其设置为 true，任何时候只要语句被调用，都会导致本地缓存和二级缓存都会被清空，默认值：false。                                                                                                                                         |
| useCache          | 将其设置为 true，将会导致本条语句的结果被二级缓存，默认值：对 select 元素为 true。                                                                                                                                                     |
| timeout           | 这个设置是在抛出异常之前，驱动程序等待数据库返回请求结果的秒数。默认值为 unset（依赖驱动）。                                                                                                                                           |
| fetchSize         | 这是尝试影响驱动程序每次批量返回的结果行数和这个设置值相等。默认值为 unset（依赖驱动）。                                                                                                                                               |
| statementType     | STATEMENT，PREPARED 或 CALLABLE 的一个。这会让 MyBatis 分别使用 Statement，PreparedStatement 或 CallableStatement，默认值：PREPARED。                                                                                                  |
| resultSetType     | FORWARD_ONLY，SCROLL_SENSITIVE 或 SCROLL_INSENSITIVE 中的一个，默认值为 unset （依赖驱动）。                                                                                                                                           |
| databaseId        | 如果配置了 databaseIdProvider，MyBatis 会加载所有的不带 databaseId 或匹配当前 databaseId 的语句；如果带或者不带的语句都有，则不带的会被忽略。                                                                                          |
| resultOrdered     | 这个设置仅针对嵌套结果 select 语句适用：如果为 true，就是假设包含了嵌套结果集或是分组了，这样的话当返回一个主结果行的时候，就不会发生有对前面结果集的引用的情况。这就使得在获取嵌套的结果集的时候不至于导致内存不够用。默认值：false。 |
| resultSets        | 这个设置仅对多结果集的情况适用，它将列出语句执行后返回的结果集并每个结果集给一个名称，名称是逗号分隔的。                                                                                                                               |

## insert, update 和 delete

数据变更语句 insert，update 和 delete 的实现非常接近：
执行完后会返回一个整数，标出执行后影响的记录条数
具体属性:

| **属性**                             | **描述**                                                                                                                                                                                                             |
|--|--|
| id                                   | 命名空间中的唯一标识符，可被用来代表这条语句。                                                                                                                                                                       |
| parameterType                        | 将要传入语句的参数的完全限定类名或别名。这个属性是可选的，因为 MyBatis 可以通过 TypeHandler 推断出具体传入语句的参数，默认值为 unset。                                                                               |
| parameterMap                         | 这是引用外部 parameterMap 的已经被废弃的方法。使用内联参数映射和 parameterType 属性。                                                                                                                                |
| flushCache                           | 将其设置为 true，任何时候只要语句被调用，都会导致本地缓存和二级缓存都会被清空，默认值：true（对应插入、更新和删除语句）。                                                                                            |
| timeout                              | 这个设置是在抛出异常之前，驱动程序等待数据库返回请求结果的秒数。默认值为 unset（依赖驱动）。                                                                                                                         |
| statementType                        | STATEMENT，PREPARED 或 CALLABLE 的一个。这会让 MyBatis 分别使用 Statement，PreparedStatement 或 CallableStatement，默认值：PREPARED。                                                                                |
| useGeneratedKeys                     | （仅对 insert 和 update 有用）这会令 MyBatis 使用 JDBC 的 getGeneratedKeys 方法来取出由数据库内部生成的主键（比如：像 MySQL 和 SQL Server 这样的关系数据库管理系统的自动递增字段），默认值：false。                  |
| keyProperty                          | （仅对 insert 和 update 有用）唯一标记一个属性，MyBatis 会通过 getGeneratedKeys 的返回值或者通过 insert 语句的 selectKey 子元素设置它的键值，默认：unset。如果希望得到多个生成的列，也可以是逗号分隔的属性名称列表。 |
| keyColumn                            | （仅对 insert 和 update 有用）通过生成的键值设置表中的列名，这个设置仅在某些数据库（像 PostgreSQL）是必须的，当主键列不是表中的第一列的时候需要设置。如果希望得到多个生成的列，也可以是逗号分隔的属性名称列表。      |
| databaseId                           | 如果配置了 databaseIdProvider，MyBatis 会加载所有的不带 databaseId 或匹配当前 databaseId 的语句；如果带或者不带的语句都有，则不带的会被忽略。                                                                        |


## sql 元素
这个元素可以被用来定义可重用的 SQL 代码段，可以包含在其他语句中

例，可以用来指定参数
```xml
<sql id="userColumns"> ${alias}.id,${alias}.username,${alias}.password 
</sql>

<select id="selectUsers" resultType="map">
  select
    <include refid="userColumns">
        <property name="alias" value="t1"/>
    </include>
  from t_role r where role_no = #{roleNo}
</select>
```
属性值也可以被用在 include 元素的 refid 属性里或 include 内部语句中

```xml
<sql id="sometable">
  ${prefix}Table
</sql>

<sql id="someinclude">
  from
    <include refid="${include_target}"/>
</sql>

<select id="select" resultType="map">
  select
    field1, field2, field3
  <include refid="someinclude">
    <property name="prefix" value="Some"/>
    <property name="include_target" value="sometable"/>
  </include>
</select>
```

## resultMap元素

映射器配置文件中定义
1. type属性定义JavaBean(可使用别名)
```
    <resultMap id="" type="">
        <constructor>
            <arg/>
            <idArg/>
        </constructor>
        <association property=""/>
        <collection property=""/>
        <id/>
        <result/>
        <discriminator javaType="">
            <case value=""></case>
        </discriminator>
    </resultMap>
```

 < Constructor >
该元素用于配置POJO的构造方法  

 < id>
表示那个列是主键，允许多个（联合主键）

 < reslut>
配置POJI到SQL列名的映射关系

 < id> & < result> 元素属性

|元素名称|说明|
|--|--|
|property|映射到列结果的字段或属性|
|column|SQL列|
|javaType|配置java类型|
|jdbcType|配置数据库类型|
|typeHandler|类型处理器|

### 级联

Mybatis级联分三种
1. association，一对一关系
2. collection，一对多
3. discriminator，鉴别器，根据条件关联不同的结果集

对应3个标签

## cache 元素






# 传递多个参数

对于参数，最简单就是传递一个参数,可能传入一个int类型，当然，也可以传入javabean

对于多个参数，有几种方式
1.  使用Map进行传递(弊端，可读性不强,不推荐用)
2. 注解方式传递(参数少时使用)
3. 使用JavaBean传递参数（参数多于5个使用）

##  使用Map传递参数
```
<select parmeterType="map">
```

接口
```
List<Role> findRoleByMap(Map<String, String> params);
```

参数传递
```java
Map<String, String> paramsMap = new HashMap<String, String>();
paramsMap.put("roleName", "me");
paramsMap.put("note", "te");

// 使用
roleMapper.findRoleByMap(paramsMap);
```

## 使用注解方式传递参数
接口改动如下,使用@Param("name")
```java
public List<Role> findRoleByAnnotation(@Param("roleName") String rolename, @Param("bote") String note)
```
XML无需再定义paramtertype  

#{name} 对应@Param("name")的参数

## 使用JavaBean提供参数

如定义一个JavaBean：Role

```xml
<select parameterType="Role">
```

接口

```
public List<Role> findRoleByParams(Role role);
```

会通过Role中的getter和setter方式传递参数




# 自动映射
## 自动映射设置
autoMapping Behavior可以设置成
1. NONE,取消自动映射
2. PARTIAL,默认，自动映射，没有定义嵌套结果集映射的结果集
3. FULL,自动映射任意复杂的结果集
```
    <settings>
        <setting name="autoMappingBehavior" value=""/>
    </settings>
```

只要列名与对应的POJO属性对应，即可自动映射
```
#对于role_name可以采用别名方式来映射
SELECT id, role_name as roleName...
```
此外，如果数据库规范命名，即
1. 数据库每一个单词用下划线分隔
2. POJO蚕蛹驼峰式命名

那么，可以设置mapUnderscoreToCamelCase=true,这样可以省略别名。
# SQL语句中的参数配置
SQL语句中，最简单的获取参数方式
```
#{name}
```
当然也可以指定参数类型，以确定用哪个TypeHandler
```xml
#{property,javaType=int,jdbcType=NUMERIC}
```
也可以直接指定TypeHandler
```xml
#{age,javaType=int,jdbcType=NUMERIC,typeHandler=MyTypeHandler}
```
对于数值类型，还有一个小数保留位数的设置，来确定小数点后保留的位数。
```xml
#{height,javaType=double,jdbcType=NUMERIC,numericScale=2}
```

# 存储过程的支持

通过mode属性来确定存储过程的参数:IN、OUT、INOUT  

如果 mode 为 OUT（或 INOUT），而且 jdbcType 为 CURSOR(也就是 Oracle 的 REFCURSOR)，必须指定一个 resultMap 来映射结果集 ResultMap 到参数类型。
```xml
#{department, mode=OUT, jdbcType=CURSOR, javaType=ResultSet, resultMap=departmentResultMap}
```

MyBatis 也支持很多高级的数据类型，比如结构体，但是当注册 out 参数时你必须告诉它语句类型名称
```xml
#{middleInitial, mode=OUT, jdbcType=STRUCT, jdbcTypeName=MY_TYPE, resultMap=departmentResultMap}
```

# 字符串替换

#{}: 会创建预编译语句，然后赋值  

${}: 不转义的字符串

