---
title: 迭代器模式
date: 2019 01-01 00:00:01
tags: 
- 迭代器模式
- 行为型模式
categories: 
- 设计模式
---
# 迭代器模式
概念：
- 提供一种方法顺序访问一个聚合对象中的各个元素，而又不暴露其内部的表示

使用场景：
1. 访问一个聚合对象的内容而无须暴露它的内部表示
2. 需要为聚合对象提供多种遍历方式
3. 为遍历不同的聚合结构提供一个统一的接口

优点:
1. 它支持以不同的方式遍历一个聚合对象。 
2. 迭代器简化了聚合类。
3. 在同一个聚合上可以有多个遍历
4. 在迭代器模式中，增加新的聚合类和迭代器类都很方便，无须修改原有代码。

模式构成:
![iterator](https://raw.githubusercontent.com/FameLsy/Images/master/design/iterator.png)

代码解释:
1. 迭代器接口:提供hasNext()、remove()、next()方法
2. 容器接口:主要提供一个获取迭代器的方法
3. 迭代器实现类
4. 容器实现类

迭代器接口
```java

/**
 * 迭代器接口
 * (也可以直接实现java.util.Iterator)
 * @author liisyu
 * @date 2019/2/23
 */
public interface Iterator {
    /**
     *判断是否有下一个
     * @return
     */
    boolean hasNext();

    /**
     * 获取下一个
     * @return
     */
    Object next();

    /**
     * 删除（来自java.util.Iterator的remove()）
     */
    default void remove() {
        throw new UnsupportedOperationException("remove");
    }
}
```

容器接口
```java
/**
 * 集合容器
 * 通常包含一个获取迭代器的方法createIterator()
 * @author liisyu
 * @date 2019/2/23
 */
public interface Aggregate {
    /**
     * 添加成员
     * @param obj 成员
     */
    void add(Object obj);

    /**
     * 删除成员
     * @param obj 成员
     */
    void remove(Object obj);
    /**
     * 创建迭代器
     * @return 迭代器
     */
    Iterator createIterator();
}
```

迭代器实现类
```java
/**
 * 具体迭代器
 * 负责管理目前遍历的位置
 * @author liisyu
 * @date 2019/2/23
 */
public class ConcreteIterator implements Iterator {
    private List list;
    /**
     * 记录集合位置
     */
    private int cursor =0;


    public ConcreteIterator(List list) {
        this.list = list;
    }

    @Override
    public boolean hasNext() {
        if (cursor == list.size()) {
            return false;
        }
        return true;
    }

    @Override
    public Object next() {
        Object obj = null;
        if (this.hasNext()){
            obj = this.list.get(cursor++);
        }
        return obj;
    }

    @Override
    public void remove() {

    }
}
```
容器实现类
```java
/**
 * 容器实现类
 * @author liisyu
 * @date 2019/2/23
 */
public class ConcreteAggregate implements Aggregate {
    private List list;

    public ConcreteAggregate() {
        list = new ArrayList();
    }

    @Override
    public void add(Object obj) {
        list.add(obj);
    }

    @Override
    public void remove(Object obj) {
        list.remove(obj);
    }

    @Override
    public Iterator createIterator() {
        return new ConcreteIterator(list);
    }
}
```

使用,对于遍历一个对象，变得省力
```java
/**
 * @author liisyu
 * @date 2019/2/23
 */
public class TestMain {
    public static void main(String[] args) {
        Aggregate aggregate = new ConcreteAggregate();
        aggregate.add(new Object());
        aggregate.add(new Object());
        aggregate.add(new Object());

        Iterator iterator = aggregate.createIterator();
        while (iterator.hasNext()){
            Object obj = iterator.next();
            System.out.println(obj);
        }

    }
}
```

## 空迭代器
对于一个没有内容可以遍历，该怎么写createIterator,返回一个如下的空迭代器，其hasNext()一直返回false
```java
/**
 * @author liisyu
 * @date 2019/2/23
 */
public class NullIterator implements Iterator {
    @Override
    public boolean hasNext() {
        return false;
    }

    @Override
    public Object next() {
        return null;
    }

    @Override
    public void remove() {
        throw new UnsupportedOperationException();
    }
}
```

