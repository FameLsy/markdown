<!-- ---
title: java基础：集合
date: 2018-02-02 00:00:16
tags: 
- java集合
categories: 
- JavaSe教程
--- -->

Java集合类库是接口与实现分离的;此外，在*Java　API* 文档中，有一组以Abstract开头的类，如"AbstractQueue",这些类是为类库实现者设计的。（想要实现自己的队列，扩展AbstractQueue比实现Ｑueue轻松的多）

# 集合框架中的接口

![collection2](https://raw.githubusercontent.com/FameLsy/Images/master/javase/collection2.png)



# 集合结构结合泛型

Ｃollection和Iterator都是泛型接口，这意味着可以编写任何集合类型的使用方法  
如下方法就可以检测任意集合是否包含指定元素的泛型方法

```java
public static <E> boolean comtains(Collection<E> c, Object obj){
    for(E element : c){
        if(element.equals(obj)) return ture;
    }
    return false;
}
```

Collection 已经声明了很多有用的抽象方法　　
![collection](https://raw.githubusercontent.com/FameLsy/Images/master/javase/collection.png)

# Collection接口

集合的最基本接口就是Ｃollection接口,且有两个基本方法
```java
public interface Collection<E>  extends Iterable<E>{
    // 向集合添加元素
    boolean add(E element);
    // 返回迭代器
    Iterator<E> iterator();
    ...
}
```

# 迭代器

迭代器虽然不是集合，但是他能作用于任何继承Iterable的集合上，而Collection正是继承了这个类。

Iterator是一个接口，包含４个方法
```java
public interface Iterator<E>{
    // 返回集合的下一个元素，集合末尾抛出NoSuchElementException
    E next();
    // 判断集合是否还有下一个元素
    boolean hasnext();
    // 删除上次调用ｎｅｘｔ方法返回的元素
    void remove();
    // Java　ＳＥ 8 加入的方法，可以对每个迭代器内的元素调用传入的ｌａｍbda表达式
    default void forEachRemaining(Consumer<? super E> action);
}
```

## 迭代器遍历

遍历一个元素，可以使用迭代器来完成,有两种使用方式
1. 通过hasnext()和next()方法进行遍历
2. 通过for-earch循环来完成
```java
Collection<String> aCollection = ...;
Iterator<String> iter = aCollection.iterator();
// 遍历迭代器，逐个访问元素
while(iter.hasnext()){
    String element = iter.next();
    //do something with element
}
// 使用for-each循环遍历
for(String element : aCollection){
     //do something with element
}
```

*for-each* 循环可以与任何实现了 *Iterable* 接口的对象一起工作，而 *Collction* 接口扩展了 *Iterable* 接口，这就意味着所有集合都可以使用 *for-each* 循环

此外，在Java SE 8,可以通过调用 *forEachRemaining()* 方法来完成查看所有元素,它将会对迭代器里的每个元素调用lambda表达式，直到没有为止。

```java
iter.forEachRemaining(element -> do something with element)
```

## 迭代器删除

对于使用remove()方法，必须先使用一次next()方法(删除上一个next的返回值),这点要额外注意

```java
iter.next();
iter.remove(); //ok
iter.remove();//error
```

# List、Set、Queue

List接口是一个有序集合，元素会添加到容器中的特定位置，可采用迭代器访问和整数索引访问(又被称为 *随机访问* )

Set接口类似与Connection,但是对方法的行为更为严谨。容器不能添加重复元素，需要定义equals方法和hashCode()方法

Queue: 队列结构

# Collection 具体集合


![collection3](https://raw.githubusercontent.com/FameLsy/Images/master/javase/collection3.png)

说明

![collection4](https://raw.githubusercontent.com/FameLsy/Images/master/javase/collection4.png)


## LinkedList 源码解析

todo

## AarrayList 源码解析

从类的定义可以看出，该类继承于AbstractList，可以随机访问、克隆、序列化
```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
```

再来看它的属性
```java
    //默认的初始化大小
    private static final int DEFAULT_CAPACITY = 10;

    //一个空的数组
    private static final Object[] EMPTY_ELEMENTDATA = {};

    //用于默认大小的空数组
    private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

    
    //存储ArrayList元素的数组缓冲区。
    transient Object[] elementData;

    //集合大小
    private int size;

```

添加方法

```java
    public boolean add(E e) {
        ensureCapacityInternal(size + 1);  
        elementData[size++] = e;
        return true;
    }
//
  private void ensureCapacityInternal(int minCapacity) {
        ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
    }
```

