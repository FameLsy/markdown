<!-- ---
title: java并发(1)：并发基础
tags: 
- 并发基础
categories: 
- java并发
--- -->

# 什么是线程
现代操作系统在运行一个程序时，会为其创建一个进程。  

现代操作系统调度的最小单元是线程，也叫轻量级进程。  

在一个进程里可以创建多个线程，这些线程都拥有各自的计数器、堆栈和局部变量等属性，并且能够访问共享的内存变量  

**打印线程**  

java本身就是多线程程序,如下程序，可以打印出一个java程序包含了哪些线程  
```java
public class MultiThread {
    public static void main(String[] args) {
        // 获取Java线程管理MXBean
        ThreadMXBean threadMXBean = ManagementFactory.getThreadMXBean();
        // 不需要获取同步的monitor和synchronizer信息，仅获取线程和线程堆栈信息
        ThreadInfo[] threadInfos = threadMXBean.dumpAllThreads(false, false);
        // 遍历线程信息，仅打印线程ID和线程名称信息
        for (ThreadInfo threadInfo : threadInfos) {
            System.out.println("[" + threadInfo.getThreadId() + "] " + threadInfo.getThreadName());
        }
    }
}

//will print 
[6] Monitor Ctrl-Break
[5] Attach Listener
[4] Signal Dispatcher
[3] Finalizer
[2] Reference Handler
[1] main
```
可以看出，不止main一个线程，而是多个线程一起执行

## 线程好处

1. 更好的利用处理器核心
2. 更快的响应时间
3. 更好的编程模型

# 线程优先级

首先了解什么是时间片？  

时间片由操作系统分出，分配给线程。当线程的时间片用完了就会发生线程调度，并等待着下次分配。线程分配到的时间片多少也就决定了线程使用处理器资源的多少。  

在Java线程中，通过一个整型成员变量priority来控制优先级

线程优先级：决定线程需要多或者少分配一些处理器资源的线程属性。

优先级的范围: 1~10,默认5

设置优先级  
```java
aThread.setPriority(int level)
```


> 注：  
> 优先级收操作系统的影响，如在ubuntu下会忽略线程优先级

## 线程状态

![thread1](https://raw.githubusercontent.com/FameLsy/Images/master/thread/thread1.png)

线程状态的变迁

![thread2](https://raw.githubusercontent.com/FameLsy/Images/master/thread/thread2.png)


>注意  
>Java将操作系统中的运行和就绪两个状态合并称为运行状态。阻塞状态是线程阻塞在进入synchronized关键字修饰的方法或代码块（获取锁）时的状态，但是阻塞在java.concurrent包中Lock接口的线程状态却是等待状态，因为java.concurrent包中Lock接口对于阻塞的实现均使用了LockSupport类中的相关方法

## 守护线程

Daemon(守护)线程是一种支持型线程,因为它主要被用作程序中后台调度以及支持性工作  

当只有守护线程时，Java虚拟机会退出  

将线程设置成守护线程
```java
aThread.setDaemon(true);
```


>注意：  
>Daemon属性需要在启动线程之前设置，不能在启动线程之后设置
>Daemon种的finally块并不一定执行(JVM提前退出)


# 启动和终止线程

## 构建线程

既然要运行线程，必然需要构建线程对象。      

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

## 启动线程
线程对象在初始化完成之后,调用start()方法就可以启动这个线程;

>注意
>启动一个线程前，最好为这个线程设置线程名称

## 中断

理解为线程的一个标识位属性，来表示该线程是否终端

中断某个线程
```java
aThread.interrupt();
```

判断线程是否中断
```java
//true  if this thread has been interrupted
aThread.isInterrupted();
```

对当前线程的中断标识位进行复位
```java
//静态方法
Thread.interrupted();
```
>注意  
>许多声明抛出InterruptedException的方法在抛出InterruptedException之前，Java虚拟机会先将该线程的中断标识位清除，此时调用isInterrupted()方法将会返回false.

中断一个线程
```java
aTHread.interrupt();
```

## 过期方法suspend()、resume()和stop()

suspend()：暂停  
resume()：恢复  
stop()：停止  

原因：无法保证能释放占有的资源，容易引发死锁

## 安全的终止线程

1. 利用中断操作=来取消或停止任务
2. 利用一个boolean变量来控制是否需要停止任务并终止该线程
3. 当run()方法结束时，线程自然终止

类设计如下
```java
private static class Runner implements Runnable {
    private long i;
    private volatile boolean on = true;
    @Override
    public void run() {
        while (on && !Thread.currentThread().isInterrupted()){
            i++;
        }
        System.out.println("Count i = " + i);
    }
    public void cancel() {
        on = false;
    }
}
//无论是使用以下的其中一种方式，都可以使run()方法的循环结束
aRunner.interrupt();
aRunner.cancel();
```

# 线程间通信

线程开始运行,拥有自己的栈空间,它们总是独立运行。而线程通讯，就是让各个线程之间相互配合完成工作。

## volatile和synchronized关键字
 
java是支持多个线程访问同一个对象或者对象的成员变量，但每个线程，拥有的是它们的拷贝.因此，这个拷贝的对象可能不是最新的

### volatile关键字

volatile
1. 访问数据时，告知程序需要从共享内存中获
2. 改变数据时，告知程序需要同步刷新回共享内存。
3. 保证所有线程对变量访问的可见性
4. 用来修饰字段（成员变量）

### synchronized关键字

synchronized
1. 确保多个线程在同一个时刻,只能有一个线程处于方法或者同步块中
2. 保证了线程对变量访问的可见性和排他性。
3. 可以修饰方法或者以同步块的形式

对于同步块的实现使用了monitorenter和monitorexit指令；同步方法则
是依靠方法修饰符上的ACC_SYNCHRONIZED。本质是对一个对象的监视器（monitor）进行获取，而这个获取过程是排他的。

监视器
1. 任意一个对象都拥有自己的监视器
2. 同步块或同步方法执行时必须获取该对象的监视器，才能进入；
3. 没有获取到监视的线程将会被阻塞在同步块和同步方法的入口处，线程进入同步队列，状态变为BLOCKED状态。

>注：  
>可见性：一个线程对共享变量值的修改，能够及实地被其他线程看到  
>排他性：即线程对块或方法的独立占有　　

## 等待/通知机制

相关方法：
![thread3](https://raw.githubusercontent.com/FameLsy/Images/master/thread/thread3.png)



等待/通知机制:  
1. 线程A调用对象O的wait()进入等待状态
2. 线程B执行完程序后，调用对象O的notify()/notifyAll()方法
3. 线程A收到通知从wait()方法返回，执行它的操作

调用wait()、notify()以及notifyAll()时需要注意的细节
1. 使用wait()、notify()和notifyAll()时需要先对调用对象加锁。
2. 调用wait()方法后,线程状态由RUNNING变为WAITING,并将当前线程放置到对象的等待队列。
3. notify()或notifyAll()方法调用后,等待线程依旧不会从wait()返回,需要调用notify()或notifAll()的线程释放锁之后,等待线程才有机会从wait()返回。
4. notify()方法将等待队列中的一个等待线程从等待队列中移到同步队列中,而notifyAll()方法则是将等待队列中所有的线程全部移到同步队列,被移动的线程状态由WAITING变为BLOCKED。
5. 从wait()方法返回的前提是获得了调用对象的锁

## 等待/通知经典范式

将线程分为
1. 等待方（消费者线程,感知变化，进行相应的操作）
2. 通知方(生产者线程,修改某个对象的值)  

等待方规则:
1. 获取锁对象
2. 如果条件不满足,那么调用对象的wait()方法,被通知后仍要检查条件。
3. 条件满足则执行对应的逻辑。

伪代码:
```java
synchronized(aObject.class) {
    while(condition) {
        aObject.wait();
    }
    doSomething;
}
```

通知方规则：
1. 获取所对象
2. 改变条件
3. 通知所有等待在对象上的线程。

伪代码：
```java
synchronized(aObject.class) {
    change condition to true;
    aObject.notifyAll();`
}
```

## 管道输入/输出流

主要用于线程之间的数据传输,而传输的媒介为内存

管道输入/输出流主要包括了如下4种具体实现:
1. PipedOutputStream
2. PipedInputStream、
3. PipedReader
4. PipedWriter

输入/输出流必须绑定起来,也就是调用connect()方法,否则会抛出异常

```java
//将输出流和输入流进行连接,否则在使用时会抛出IOException
aPipedWriter.connect(aPipedReader);
```

## Thread.join()的使用

含义：
1. 线程A拥有线程B的索引，并再内部方法调用了线程B的threadB.join()
2. 线程A必须等待线程B终止，才能从threadB.join()方法返回
4. join()内部利用了通知/等待机制

此外还有join(long millis)和join(long millis,int nanos):到达指定时间后，返回。

join()的部分源码
```java
// 加锁当前线程对象
public final synchronized void join() throws InterruptedException {
    // 条件不满足,继续等待
    while (isAlive()) {
        wait(0);
    }
// 条件符合,方法返回
}
```

可以发现，符合先前通知/等待经典范式的加锁、通知、处理逻辑三个步骤

## ThreadLocal的使用
ThreadLocal,即*线程变量(泛型)*,是一个以ThreadLocal对象为键、任意对象为值的存储结构；一个线程可以根据一个ThreadLocal对象查询到绑定在这个线程上的一个值

```java
// 创建
ThreadLocal<T> aThreadLocal = new ThreadLocal<T>();
// 设置值
aThreadLocal.set(T);
// 获取值
aThreadLocal.get();
```

