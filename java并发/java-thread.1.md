<!-- 线程基础 -->

# 什么是线程
现代操作系统在运行一个程序时，会为其创建一个进程。  

现代操作系统调度的最小单元是线程，也叫轻量级进程。  

在一个进程里可以创建多个线程，这些线程都拥有各自的计数器、堆栈和局部变量等属性，并且能够访问共享的内存变量  

线程好处
1. 更好的利用处理器核心
2. 更快的响应时间
3. 更好的编程模型

# 线程的安全性

想要编写线程安全的代码，核心在于要对*状态访问操作*进行管理，特别是对共享的和可变的状态的访问

所谓的对线的状态，指的是存储在状态变量(如实例、静态域)中的数据，当然也可能包括其他以来对象的域(如HashMap的状态不仅是其本身，还存储在多个Map.Entry对象中)

*共享*意味着状态变量可以由多个线程同时访问，而*可变*意味着状态变量的值在生命周期内发生变化，当多个线程对一个对象的可变状态变量进行访问时，就可能引发线程安全性问题；所以必须得采用同步机制来协同这些线程对状态变量的访问；

java主要的同步机制有
1. synchronized 关键字
2. volatile 
3. Lock
4. 原子变量

# 创建线程

既然要运行线程，必然需要创建线程对象，java有两种创建线程的方式
1. 通过java.lang.Runnable接口(推荐)
1. 继承java.lang.Thread类

## 通过java.lang.Runnable接口(推荐)

Runnable接口只有一个run()方法，是一个函数式接口，所以可以运用lambda表达式
```java
//Runnable接口源码
@FunctionalInterface
public interface Runnable {
    public abstract void run();
}
```

具体实现方式为
```java
//使用lambda表达式，省去了实现Runnable接口
//task code 写run()方法的实际逻辑处理
Runnable runnable = () ->{task code}
Thread threadA = new Thread(runnable);
Thread threadB = new Thread(runnable);
```

## 继承java.lang.Thread

具体实现方式为
```java
//继承Thread并重写run()方法
public class MyThread extends Thread {
    @Override
    public void run() {
        task code
    }
}
```

## 两种方式的区别

为什么要选择通过java.lang.Runnable接口的方式创建线程？
1. 将运行机制和任务进行分开解耦,Thread类负责运行机制，Runnable接口负责任务方法
2. 减少线程开销，如果使用方式二，对于每个任务都需要创建一个线程；而方式一可以通过传入不同的Runnable,来实现一个线程执行不同任务

## Thread 源码解析

如下是java.lang.Thread的构造器的部分函数
```java
private Thread(ThreadGroup g, Runnable target, String name,long stackSize, AccessControlContext acc,boolean inheritThreadLocals) {
    ...
    if (name == null) {
        throw new NullPointerException("name cannot be null");
    }
    // 当前线程就是该线程的父线程
    Thread parent = currentThread();
    this.group = g;
    // 将daemon、priority属性设置为父线程的对应属性
    this.daemon = parent.isDaemon();
    this.priority = parent.getPriority();
    this.name = name.toCharArray();
    this.target = target;
    setPriority(priority);
    // 将父线程的InheritableThreadLocal复制过来
    if (parent.inheritableThreadLocals != null)
    this.inheritableThreadLocals=ThreadLocal.createInheritedMap(parent.
    inheritableThreadLocals);
    // 分配一个线程ID
    tid = nextThreadID();
    ...
}
```
可以看出：
1. 一个新构造的线程对象是由其parent线程来进行空间分配的
2. child线程继承了parent是否为Daemon、优先级和加载资源的contextClassLoader以及可继承的ThreadLocal,同时还会分配一个唯一的ID来标识这个child线程

>注意  
>启动一个线程前，最好为这个线程设置线程名称  
>这么做的好处式在使用jstack分析程序或者进行问题排查时，就会给开发人员提供一些提示

```java
//java提供了为线程取名的构造函数
public Thread(Runnable target, String name)
```

# 启动线程

线程对象在初始化完成之后,调用如下方法启动线程
```
aThread.start()
```

>注意  
>aThread.run()并不是启动线程，只是运行了run()方法  


# 中断线程

线程在以下两种情况下会终止
1. run方法执行到最后一句，并且return返回
2. 出现了方法中没有捕获的异常

以上的两种终止方式，都是被动的去终止，那么如何手动的去终止？
1. 使用stop()，已经废弃
2. 使用interrupt()方法请求中断线程

中断某个线程
```java
aThread.interrupt();
```

中断理解为线程的一个标识位属性，来表示该线程是否被中断；

判断线程是否中断
```java
//如果线程中断，则返回true；但如果线程已经终止，那么无论是否被中断过，都会返回false
aThread.isInterrupted();
```

对当前线程的中断标识位进行复位
```java
//静态方法，中断复位
Thread.interrupted();
```

此外，许多声明抛出InterruptedException的方法在抛出InterruptedException之前，Java虚拟机会先将该线程的中断标识位清除

# 线程其他方法

使线程休眠
```java
//单位毫秒。抛出InterruptedException异常
 public static native void sleep(long millis) throws InterruptedException;
 ```

返回代表当前执行线程的对象
```java
//静态方法
Thread.currentThread();
```

过期方法,原因是无法保证能释放占有的资源，容易引发死锁
```java
suspend()：暂停  
resume()：恢复  
stop()：停止  
```