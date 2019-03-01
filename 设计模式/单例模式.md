---
title: 单例模式
date: 2019 01-01 00:00:01
tags: 
- 单例模式
- 创建型模式
categories: 
- 设计模式
---
概念：  
1. 对象只有一个实例
2. 静态变量也可以实现单例，但越是耗资源的对象，就越浪费（如果只在需要时创建，就可以避免这种浪费）
3. 分懒汉式和饿汉式

使用场景：
1. 对于线程池、缓存、对话框、注册表、日志对象、充当打印机等，这些需要保证只有一个实例对象
2. 

优点:
1. 对于频繁使用的对象，可以省略创建对象所花费的时间，这对于那些重量级对象而言，是非常可观的一笔系统开销；
2. 由于 new 操作的次数减少，因而对系统内存的使用频率也会降低，这将减轻 GC 压力，缩短 GC 停顿时间。

# 饿汉式
1. 饿汉式（线程安全）
2.  饿汉式（枚举实现）
全局的单例实例在类装载时构建

## 饿汉式（线程安全）
```java
/**
 * 饿汉式线程安全
 * @author liisyu
 * @date 2019/02/22
 */
public class Hungry {

    /**
     * 静态初始化变量，保证单例且线程安全
     */
    private static Hungry hungry = new Hungry();

    /**
     * 私有化构造器，防止创建
     */
    private Hungry(){};
    public static Hungry getHungry() {
        return hungry;
    }

    public void doMethod(){
        System.out.println("do something");
    }
}
```

## 饿汉式（枚举实现）
```java
/**
 * 饿汉式枚举
 * @author liisyu
 * @date 2019/02/22
 */
public enum HungryEnum {
    /**
     * 唯一实体类
     */
    HUNGRY_INSTANCE;

    public void doMethod(){
        System.out.println("do something");
    }
}

```

# 懒汉式
1. 懒汉式（非线程安全)
2. 懒汉式（线程安全）
3. 懒汉式(双重检查加锁版本)
4. 懒汉式（登记式/静态内部类方式）


## 懒汉式（非线程安全)
```
/**
 * 懒汉式非线程安全
 * @author liisyu
 * @date 2019/02/22
 */
public class Lazy {
    private static Lazy lazy;

    private Lazy(){};

    public static Lazy getLazy(){
        if (lazy == null){
            //这里有可能出现线程问题
            lazy = new Lazy();
        }
        return lazy;
    }
}
```
## 懒汉式（线程安全）
如果想要线程安全，可以使用synchronized,不过每次获取都需要经过一遍锁，不推荐
```
   public static synchronized Lazy getLazy(){
        if (lazy == null){
            //这里有可能出现线程问题
            lazy = new Lazy();
        }
        return lazy;
    }
```

##  懒汉式(双重检查加锁版本)
1. 第一次先检查是否为null，再进入同步
2. 第二次检查是否为Null，null才创建实例。
```java
/**
 * 懒汉式(双重检查加锁版本)
 * @author liisyu
 * @date 2019/02/22
 */
public class LazyDoubleLock {
    /**
     * volatile保证，当uniqueInstance变量被初始化成Singleton实例时，多个线程可以正确处理uniqueInstance变量
     */
    private volatile static LazyDoubleLock lazy;
    
    private LazyDoubleLock() {
    }
    
    public static LazyDoubleLock getInstance() {
        //检查实例，如果不存在，就进入同步代码块
        if (lazy == null) {
            //只有第一次才彻底执行这里的代码
            synchronized(LazyDoubleLock.class) {
                //进入同步代码块后，再检查一次，如果仍是null，才创建实例
                if (lazy == null) {
                    lazy = new LazyDoubleLock();
                }
            }
        }
        return lazy;
    }
}

```
## 懒汉式（登记式/静态内部类方式）
只有通过显式调用 getInstance 方法时，才会显式装载 SingletonHolder 类(只有第一次使用这个单例的实例的时候才加载，同时不会有线程安全问题)
```java
/**
 * 懒汉式(静态内部类方式)
 * @author liisyu
 * @date 2019/02/22
 */
public class LazyInnerClass {
    private static class SingletonHolder {
        private static final LazyInnerClass LZAY = new LazyInnerClass();
    }
    private LazyInnerClass (){}

    public static final LazyInnerClass getInstance() {
        return SingletonHolder.LZAY;
    }
}
```