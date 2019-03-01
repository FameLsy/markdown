---
title: 模板方法模式
date: 2019 01-01 00:00:01
tags: 
- 模板方法模式
- 行为型模式
categories: 
- 设计模式
---
# 模板方法模式

概念：
- 在一个方法中定义一个算法的骨架，而将一些步骤延迟到子类中，模板方法使得子类可以再不改变算法结构的情况下，重新定义算法中的某些步骤

使用场景：
- 子类拥有多个共同的方法（算法不同），但这些方法执行的逻辑顺序相同

优点:
1. 封装不变部分，扩展可变部分
2. 提取公共代码，便于维护
3. 行为父类控制，子类实现

模式构成:
![template](https://raw.githubusercontent.com/FameLsy/Images/master/design/template.png)

代码解释:
1. 抽象类：将算法逻辑提取到模板方法，具体算法由子类提供
2. 子类实现：重写抽象类的抽象方法

抽象类
1. 模拟高达拼装过程，不同高达具体怎么拼装是不同的，但都是按先拼装头部(primitiveOperation1)，拼装四肢(primitiveOperation2),拼装完成(primitiveOperation3)这3个步骤
2. 抽象类也实现了一个不属于子类方法但必须的通用方法，高达出厂(concreteOperation)
3. 对于由子类实现的方法必须声明抽象

```java
/**
 * 抽象类
 *
 * @author liisyu
 * @date 2019/2/23
 */
public abstract class AbstractClass {
    /**
     * 模板方法
     */
    final void templateMethod(){
        primitiveOperation1();
        primitiveOperation2();
        primitiveOperation3();
        concreteOperation();
    }

    /**
     * 原始操作1
     * 属于子类操作的方法必须抽象
     */
    abstract void primitiveOperation1();

    /**
     * 原始操作2
     * 属于子类操作的方法必须抽象
     */
    abstract void primitiveOperation2();

    /**
     * 原始操作3
     * 属于子类操作的方法必须抽象
     */
    abstract void primitiveOperation3();

    /**
     * 属于本类的操作
     */
    void concreteOperation(){
        System.out.println("出厂");
    };
}
```

具体子类
```java
/**
 * 具体的子类
 * @author liisyu
 * @date 2019/2/23
 */
public class GaoDaClass extends AbstractClass {
    @Override
    void primitiveOperation1() {
        System.out.println("拼装TM合金头部");
    }

    @Override
    void primitiveOperation2() {
        System.out.println("拼装GDM四肢");
    }

    @Override
    void primitiveOperation3() {
        System.out.println("高达拼装完成");
    }
}

/**
 * 具体的子类
 * @author liisyu
 * @date 2019/2/23
 */
public class ZhaGuClass extends AbstractClass {
    @Override
    void primitiveOperation1() {
        System.out.println("拼装SM型头部");
    }

    @Override
    void primitiveOperation2() {
        System.out.println("拼装E3-DN四肢");
    }

    @Override
    void primitiveOperation3() {
        System.out.println("扎古拼装完成");
    }
}

```

使用,按照相同的逻辑顺序，拼装出了不同的高达
```java
/**
 * @author liisyu
 * @date 2019/2/23
 */
public class TestMain {
    public static void main(String[] args) {
        AbstractClass concreteClass = new GaoDaClass();
        concreteClass.templateMethod();

        concreteClass = new ZhaGuClass();
        concreteClass.templateMethod();
    }
}
//print
拼装TM合金头部
拼装GDM四肢
高达拼装完成
出厂
拼装SM型头部
拼装E3-DN四肢
扎古拼装完成
出厂
```
