

<!-- # Java中的锁 -->

锁能够防止多个线程同时访问共享资源.但有些锁可以允许多个线程并发的访问共享资源，比如读写锁


# Lock 接口

Java SE 5之后，并发包中新增了Lock接口,在使用时需要显式地获取和释放锁。

>注意  
>不要将获取锁的过程写在try块中，因为如果在获取锁（自定义锁的实现）时发生了异常，异常抛出的同时，也会导致锁无故释放

Lock接口提供的synchronized关键字不具备的主要特性

![thread4](https://raw.githubusercontent.com/FameLsy/Images/master/thread/thread4.png)

Lock的API

![thread5](https://raw.githubusercontent.com/FameLsy/Images/master/thread/thread5.png)

# 重入锁(ReentrantLock)

支持重进入的锁，它表示该锁能够支持一个线程对资源的重复加锁。

为什么要用到重入锁，如下场景
1. 线程A通过aLock.lock()获取了锁
2. 锁未被释放，线程A再次aLock.lock()获取锁，此时将无法获取，导致自己被自己阻塞


除此之外，该锁的还支持获取锁时的公平和非公平性选择
1. 公平性：锁获取是顺序的，先请求锁的先获取锁
2. 非公平性: 效率比公平性锁高

## 实现重进入

线程在获取到锁之后能够再次获取该锁而不会被锁所阻塞,需要解决两个问题
1. 线程再次获取锁,判断当前线程是否为锁的占有者，是，则获取成功
2. 锁的最终释放,利用计数器记录锁获取的次数，每次释放时计数器自减，当为0时表示锁成功释放

ReentrantLock默认为以非公平性实现
```java
//获取锁
//判断当前线程是否为获取锁的线程来决定获取操作是否成功
//重复获取只是添加了同步状态值
final boolean nonfairTryAcquire(int acquires) {
    final Thread current = Thread.currentThread();
    int c = getState();
    if (c == 0) {
        if (compareAndSetState(0, acquires)) {
            setExclusiveOwnerThread(current);
            return true;
        }
    } else if (current == getExclusiveOwnerThread()) {//通过判断当前线程是否跟获取锁的线程是同一个线程
        int nextc = c + acquires;
        if (nextc < 0)
            throw new Error("Maximum lock count exceeded");
        //则将同步状态值进行增加并返回true,表示获取成功,相当于成功获取锁的线程再次获取锁，只是增加了同步状态值
        setState(nextc);
        return true;
    }
    return false;
}
//释放锁
//前n-1次方法必定返回false，只有同步状态为0时，才返回true
protected final boolean tryRelease(int releases) {
    int c = getState() - releases;
    if (Thread.currentThread() != getExclusiveOwnerThread())
        throw new IllegalMonitorStateException();
    boolean free = false;
    if (c == 0) {
        free = true;
        setExclusiveOwnerThread(null);
    }
    setState(c);
    return free;
}
```

ReentrantLock公平性实现
```java
//区别在于判断条件多了hasQueuedPredecessors(),判断出当前线程所对应的节点是否有前驱节点，如果有，则返回true
//也就是说，只有前驱线程获取并释放锁之后才能继续获取锁
protected final boolean tryAcquire(int acquires) {
    final Thread current = Thread.currentThread();
    int c = getState();
    if (c == 0) {
        if (!hasQueuedPredecessors() && compareAndSetState(0, acquires)) {
            setExclusiveOwnerThread(current);
            return true;
        }
    } else if (current == getExclusiveOwnerThread()) {
        int nextc = c + acquires;
        if (nextc < 0)
            throw new Error("Maximum lock count exceeded");
        setState(nextc);
        return true;
    }
    return false;
}

//AQS中的方法
public final boolean hasQueuedPredecessors() {

        Node t = tail; 
        Node h = head;
        Node s;
        //判断头节点和尾节点是否是同一节点，如果h == t,说明同步队列没有节点，肯定没有前驱节点
        // 第二步判断，如果头节点的后继节点为空(只有一个节点，自然没有前驱节点) 或者 有后继节点但判断后继节点对应的线程是否是当前线程
        //通过三个判断来判断出当前线程的是否是下一个需要执行的
        return h != t &&
            ((s = h.next) == null || s.thread != Thread.currentThread());
    }
```

非公平性锁的一个线程连续获取锁的情况  
- 原因为刚释放锁的线程再次获取同步状态的几率会非常大，使得其他线程只能在同步队列中等待,可能造成线程饥饿（使得其他线程只能在同步队列中等待）

为什么非公平锁为默认？
- 公平锁锁保证了锁的获取按照FIFO原则，而代价是进行大量的线程切换。非公平性锁虽然可能造成线程“饥饿”，但极少的线程切换，保证了其更大的吞吐量。

# 读写锁

读写锁在同一时刻可以允许多个读线程访问，但是在写线程访问时，所有的读线程和其他写线程均被阻塞;读写锁维护了一对锁，一个读锁和一个写锁，通过分离读锁和写锁，使得并发性相比一般的排他锁有了很大提升

java 5.0之前是如何做的?
- 使用Java的等待通知机制
- 写操作开始，其他读操作等待，直到通知(防止幻读)

有了读写锁后怎么做？
- 读操作，获取读锁
- 写操作，获取写锁,后续其他线程读写操作均被阻塞

## ReentrantReadWriteLock

Java并发包提供读写锁的实现是ReentrantReadWriteLock,其特性如下

![lock](https://raw.githubusercontent.com/FameLsy/Images/master/thread/lock.png)

ReadWriteLock仅定义了获取读锁和写锁的两个方法，即readLock()方法和writeLock()方法，而ReentrantReadWriteLock，还提供了一些便于外界监控其内部工作状态的方法

![lock2](https://raw.githubusercontent.com/FameLsy/Images/master/thread/lock2.png)

读写锁的实现分析