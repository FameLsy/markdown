<!-- Volatile域 -->


# volatile关键字

java是支持多个线程访问同一个对象或者对象的成员变量，但每个线程，拥有的是它们的拷贝.因此，这个拷贝的对象可能不是最新的。当然，如果使用锁机制的话肯定没问题，但如果仅仅为了读写一两个实例域而使用锁，开销就显得有点大了。volatile关键字就是为实例域提供了一种免锁的机制。

volatile
1. 访问数据时，告知程序需要从共享内存中获取
2. 改变数据时，告知程序需要同步刷新回共享内存。
3. 保证所有线程对变量访问的可见性，但不保证其原子性

如果想要使用volatile来打到线程安全，必须同时满足下面两个条件
1. 对变量的写操作不依赖于当前值(如x++,其操作其实是读->添加->赋值三个操作，需要保证原子性，所以不能使用volatile)
2. 该变量没有包含在具有其他变量的不变式中

# 正确使用 volatile 的模式

## 状态标志

实现 volatile 变量的规范使用仅仅是使用一个布尔状态标志，用于指示发生了一个重要的一次性事件

```java
volatile boolean shutdownRequested;
 
...
 
public void shutdown() { shutdownRequested = true; }
 
public void doWork() { 
    //每次循环，都会读取最新的shutdownRequested值，如果使用synchronized,就需要加锁，阻塞、唤醒等一些列操作
    while (!shutdownRequested) { 
        // do stuff
    }
}
```

## 一次性安全发布

对于以下语句，实际上又三个步骤
1. 分配内存空间
2. 初始化对象
3. 对象指向内存空间

```java
theFlooble = new Flooble();
```

但实际上，2，3可能重排，变为
1. 分配内存空间
2. 对象指向内存空间
3. 初始化对象

再看如下代码
1. Thread A进行initInBackground()时，到达第二步，对象指向内存空间
2. Thread B在此时读取theFlooble,不为空，但对象却还没初始化
3. 使用了volatile关键字后，重排序被禁止，所有的写（write）操作都将发生在读（read）操作之前

```java
//Thread A
public class BackgroundFloobleLoader {
    public volatile Flooble theFlooble;
 
    public void initInBackground() {
        // do lots of stuff
        theFlooble = new Flooble();  // this is the only write to theFlooble
    }
}
 
 // Thread B
public class SomeOtherClass {
    public void doWork() {
        while (true) { 
            // do some stuff...
            // use the Flooble, but only if it is ready
            if (floobleLoader.theFlooble != null) 
                doSomething(floobleLoader.theFlooble);
        }
    }
}
```

