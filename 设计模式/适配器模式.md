---
title: 适配器模式
date: 2019 01-01 00:00:01
tags: 
- 适配器模式
- 结构型模式
categories: 
- 设计模式
---
# 适配器模式
概念：
-. 将原本接口不兼容的类可以一起工作


这张图可以很好解释适配器

![适配器](https://raw.githubusercontent.com/FameLsy/Images/master/design/适配器.png)

使用场景：  
1. 有动机地修改一个正常运行的系统接口，用于解决正在运行的项目问题
2. 系统需要使用一些现有的类，而这些类的接口（如方法名）不符合系统的需要，甚至没有这些类的源代码。

优点:
1. 可以将两个没有关联的类一起运行
2. 提高了类的复用
3. 添加类的透明度
4. 灵活性好

缺点
1. 过多适配器会导致系统混乱

模式构成:
![adapter](https://raw.githubusercontent.com/FameLsy/Images/master/design/adapter.png)

代码解释:
1. 目标接口(Target)
2. 被适配的对象(adaptee)
3. 适配器(Adapter):连接1，2，所有委托都给被适配的对象

目标接口
```java
/**
 * 目标接口
 *
 * @author liisyu
 * @date 2019/2/23
 */
public interface Target {
    /**
     * 客户端请求处理的方法
     */
    void request();
}

```
被适配的对象
```java
/**
 * 被适配的对象
 * @author liisyu
 * @date 2019/2/23
 */
public class Adaptee {
    /**
     * 原本存在的方法
     */
    public void specificRequest(){
        System.out.println("被适配的对象方法");
    }
}

```

适配器
```java
/**
 * 适配器
 * @author liisyu
 * @date 2019/2/23
 */
public class Adapter implements Target{
    private Adaptee adaptee;

    /**
     * 传入被适配的对象
     * @param adaptee 需要被适配的对象
     */
    public Adapter(Adaptee adaptee) {
        this.adaptee = adaptee;
    }

    @Override
    public void request() {
        adaptee.specificRequest();
    }
}

```

使用
```java
/**
 * 客户端
 * @author liisyu
 * @date 2019/2/23
 */
public class Client {
    public static void main(String[] args) {
//        创建需要被适配的对象
        Adaptee adaptee = new Adaptee();
//        创建适配器
        Target target = new Adapter(adaptee);
//        请求处理
        target.request();

    }
}
```

## JDK中的适配器
```java
java.util.Arrays#asList()
java.io.InputStreamReader(InputStream)
java.io.OutputStreamWriter(OutputStream)
```

Java I/O 库大量使用了适配器模式，如 ByteArrayInputStream 是一个适配器类，它继承了 InputStream 的接口，并且封装了一个 byte 数组。换言之，它将一个 byte 数组的接口适配成 InputStream 流处理器的接口。

在 OutputStream 类型中，所有的原始流处理器都是适配器类。ByteArrayOutputStream 继承了 OutputStream 类型，同时持有一个对 byte 数组的引用。它一个 byte 数组的接口适配成 OutputString 类型的接口，因此也是一个对象形式的适配器模式的应用。

FileOutputStream 继承了 OutputStream 类型，同时持有一个对 FileDiscriptor 对象的引用。这是一个将 FileDiscriptor 接口适配成 OutputStream 接口形式的对象型适配器模式。

Reader 类型的原始流处理器都是适配器模式的应用。StringReader 是一个适配器类，StringReader 类继承了 Reader 类型，持有一个对 String 对象的引用。它将 String 的接口适配成 Reader 类型的接口。
