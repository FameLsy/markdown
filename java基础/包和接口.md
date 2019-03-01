---
title: java 基础:包和接口
date: 2018-02-02 00:00:11
tags: 
- 包和接口
categories:
- JavaSe教程
---

# 包
包既是命名机制也是可见度控制机制  

对于包的访问控制，可以查看（7）修饰符  

## 定义包
```java
package pkg;
```
如果省略package 语句,类名被输入一个默认的没有名称的包

## 类路径
Java编译器考虑的特定位置作为包层次的根被类路径(CLASSPATH)控制

将类路径设置顶层类层次


# 接口

接口方法都是public,可以不写。但是实现接口的时候必须写（实现类不写是default）

## 接口特性

1. 无法实例化
2. 能声明接口的变量
3. 可以使用 instanceof 检查类是否属于某个特定的接口
4. 接口可以继承接口
5. 接口不能包含实例域和静态方法(java 8.0后可以添加静态方法)
6. 接口可以包含常量（共享，因为是public static final）
7. 接口方法自动设置为public abstract,接口域自动设置为public static final

## 接口与抽象类

为什么要引入接口？  
接口可以多实现，而抽象类只能单继承

## 默认方法

可以为接口方法提供一个默认方法,使用default 修饰符标记
```java
public interface Comparable<T>{
    default int compareTo(T other){return 0;}
}
```
一般情况下没什么用(实现Comparable接口时会覆盖这个方法),但可以在如下环境使用：

这样，把所有方法都设置为默认，程序员只需覆盖关心的方法即可
```java
public interface Demo(){
    default void method1(){}
    default void method2(){}
    default void method3(){}
    default void method4(){}
    default void method5(){}
}
```

默认方法的另一个重要用途： *接口演化*  

假设我们现在有一个接口 A, Mas是其的一个实现类
```java
public interface A{...}
publiic Class Max implements A{...}
```
后来，我们为A添加了一个新方法 newMethod
```java
public interface A{
    void newMethod(){}
}
```
可能会出现以下情况  
1. Mas类编译将会失败（没有重写newMethod）
2. 不重新编译Mas类，在Mas调用newMethod时抛出AbstractMethodError

如果变为defalut方法，以上两个问题都会解决  
1. Mas成功编译
2. 不重新编译Mas,调用时会向上调用A接口的newMethod

## 解决默认方法冲突
　
情况一：接口与父类冲突：父类优先  
如接口定义了默认方法getName(),而父类中也定义了getName()方法，默认方法被忽略  

情况二：接口与接口冲突： 手动覆盖  
如接口A、Ｂ同时定义了默认的getName()方法，必需覆盖解决冲突。  
也可以在类中选择二者其一
```java
Class Choose implements A,B{
    public String getName(){
        return A.super.getName();
    }
}　
```

情况三：如果接口Ａ、Ｂ的方法是非默认方法，无论是否另一个有同名默认方法，必需重写