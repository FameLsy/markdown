---
title: 解决命名空间无法注入集合的方式
tags: 
- 解决命名空间无法注入集合的方式
categories: 
- Spring 
---

# 解决命名空间无法注入集合的方式

使用< util >标签(需要加约束),如
```xml
创建了一个集合的对象
    <util:list id="stringList">
        <value>a</value>
        <value>b</value>
        <value>c</value>
        <value>d</value>
    </util:list>

```

Spring util-命名空间中的元素
|元素|描 述|
|--|--|
|util:constant	|引用某个类型的public static域，并将其暴露为bean|
|util:list	|创建一个java.util.List类型的bean，其中包含值或引用|
|util:map|	创建一个java.util.Map类型的bean，其中包含值或引用|
|util:properties|	创建一个java.util.Properties类型的bean|
|util:property-path|	引用一个bean的属性（或内嵌属性），并将其暴露为bean|
|util:set|	创建一个java.util.Set类型的bean，其中包含值或引用|

