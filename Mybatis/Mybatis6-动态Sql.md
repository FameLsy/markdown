---
title: Mybatis动态sql
date: 2019-02-16 00:00:06
tags: 
- Mybatis动态sql
categories: 
- Mybaits
---
Mybaits包含以下几种动态Sql元素

|元素|描述|
|--|--|
|if|判断语句|
|choose(when,otherwise)|相当于java中的case，when|
|trim(where,set)|辅助元素|
|foreach|循环语句|

# < if >元素
```xml
<select id="findActiveBlogWithTitleLike"
     resultType="Blog">
  SELECT * FROM BLOG 
  WHERE state = ‘ACTIVE’ 
  <if test="title != null">
    AND title like #{title}
  </if>
</select>
```

两个以上的参数
```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’ 
  <if test="title != null">
    AND title like #{title}
  </if>
  <if test="author != null and author.name != null">
    AND author_name like #{author.name}
  </if>
</select>
```

# <choose, when, otherwise >

多个条件选一个
```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <choose>
    <when test="title != null">
      AND title like #{title}
    </when>
    <when test="author != null and author.name != null">
      AND author_name like #{author.name}
    </when>
    <otherwise>
      AND featured = 1
    </otherwise>
  </choose>
  </select>
  ```

# <where, set >

where 元素只会在至少有一个子元素的条件返回 SQL 子句的情况下才去插入“WHERE”子句。而且，若语句的开头为“AND”或“OR”，where 元素也会将它们去除。

```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG 
  <where> 
    <if test="state != null">
         state = #{state}
    </if> 
    <if test="title != null">
        AND title like #{title}
    </if>
    <if test="author != null and author.name != null">
        AND author_name like #{author.name}
    </if>
  </where>
</select>
```

# < trim >

prefixOverrides 属性会忽略通过管道分隔的文本序列（注意此例中的空格也是必要的）。  
它的作用是移除所有指定在 prefixOverrides 属性中的内容，并且插入 prefix 属性中指定的内容。

以下语句等价于< where>
```xml
<trim prefix="WHERE" prefixOverrides="AND |OR ">
  ... 
</trim>
```

# < set >

类似的用于动态更新语句的解决方案叫做 set。set 元素可以用于动态包含需要更新的列，而舍去其它的

```xml
<update id="updateAuthorIfNecessary">
  update Author
    <set>
      <if test="username != null">username=#{username},</if>
      <if test="password != null">password=#{password},</if>
      <if test="email != null">email=#{email},</if>
      <if test="bio != null">bio=#{bio}</if>
    </set>
  where id=#{id}
</update>
```

set 元素会动态前置 SET 关键字，同时也会删掉无关的逗号，因为用了条件语句之后很可能就会在生成的 SQL 语句的后面留下这些逗号

等价于
```xml
<trim prefix="SET" suffixOverrides=",">
  ...
</trim>
```

# < foreach>

```xml
<select id="selectPostIn" resultType="domain.blog.Post">
  SELECT *
  FROM POST P
  WHERE ID in
  <foreach item="item" index="index" collection="list"
      open="(" separator="," close=")">
        #{item}
  </foreach>
</select>
```

|属性|描述|
|--|--|
|collection|表示传递进来的参数，可以是List，Set等集合|
|item|循环中的当前元素|
|index|当前元素索引|
|open/close|以什么符号元素包裹起来|
|separator|元素主键的间隔符号|

# < bind >
bind 元素可以从 OGNL 表达式中创建一个变量并将其绑定到上下文

_parameter.getTitle()表示的是传进来的参数
```xml
<select id="selectBlogsLike" resultType="Blog">
  <bind name="pattern" value="'%' + _parameter.getTitle() + '%'" />
  SELECT * FROM BLOG
  WHERE title LIKE #{pattern}
</select>
```

## bind的使用

定义接口
```java
public List<T> findRole(@Param("name")String name, @Param("note") String note);
```

定义bind
```xml
<select id="selectBlogsLike" resultType="Blog">
  <bind name="pattern_name" value="'%' + name + '%'" />
  <bind name="pattern_note" value="'%' + note + '%'" />
  SELECT * FROM BLOG
  WHERE title LIKE #{pattern_name} and note LIKE #{pattern_note}
</select>
```