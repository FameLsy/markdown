
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

