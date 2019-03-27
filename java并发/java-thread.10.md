

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
- 读操作，获取读锁，允许多个线程获取读锁进行访问，后继无法获取写锁
- 写操作，获取写锁,后续其他线程读写操作均被阻塞

## ReentrantReadWriteLock

Java并发包提供读写锁的实现是ReentrantReadWriteLock，它实现于ReadWriteLock,其特性如下

![lock](https://raw.githubusercontent.com/FameLsy/Images/master/thread/lock.png)

ReadWriteLock仅定义了获取读锁和写锁的两个方法，即readLock()方法和writeLock()方法，而ReentrantReadWriteLock，还提供了一些便于外界监控其内部工作状态的方法

![lock2](https://raw.githubusercontent.com/FameLsy/Images/master/thread/lock2.png)

# 读写锁的实现分析
ReentrantReadWriteLock的实现，主要包括：
1. 读写状态的设计
2. 写锁的获取与释放
3. 读锁的获取与释放
4. 锁降级

## 读写状态的设计

读写锁同样依赖自定义同步器来实现同步功能，而读写状态就是其同步器的同步状态.写锁的自定义同步器需要在同步状态（一个整型变量）上维护多个读线程和一个写线程的状态

如何在一个变量上维护多种状态？
- 使用“按位切割”
- 高16位表示读，低16位表示写

![lock3](https://raw.githubusercontent.com/FameLsy/Images/master/thread/lock3.png)

如何确定状态?
- 通过位运算
- 写状态：status_value&0x0000FFFF(高16位全部抹去)
- 写状态+1：status_value +1
- 读状态：status_value>>>16(无符号补0右移16位)
- 读状态+1：status_value+(1<<16)，也就是status_value+0x00010000

## 写锁的获取与释放

写锁是一个支持重进入的排它锁
- 如果当前线程已经获取了写锁，则增加写状态
- 如果当前线程在获取写锁时，读锁已经被获取其他或当前线程获取（读状态不为0）或者该线程不是已经获取写锁的线程，则当前线程进入等待状态

```java
//写锁获取
protected final boolean tryAcquire(int acquires) {
    Thread current = Thread.currentThread();
    int c = getState();
    //获取写锁状态
    int w = exclusiveCount(c);
    if (c != 0) {
        //从c != 0->当前线程存在锁，w == 0,没有写锁，说明存在的是读锁
        // 存在读锁或者当前获取线程不是已经获取写锁的线程
        if (w == 0 || current != getExclusiveOwnerThread())
            return false;
        if (w + exclusiveCount(acquires) > MAX_COUNT)
            throw new Error("Maximum lock count exceeded");
        setState(c + acquires);
        return true;
    }
    if (writerShouldBlock() || !compareAndSetState(c, c + acquires)) {
        return false;
    }
    setExclusiveOwnerThread(current);
    return true;
}
```

写锁释放与ReentrantLock类似

## 读锁的获取与释放

读锁是一个支持重进入的共享锁，它能够被多个线程同时获取,在没有其他*写线程*访问（或者写状态为0）时，读锁总会被成功地获取;如果当前线程在获取读锁时，写锁已被其他线程获取，则进入等待状态

逻辑如下：
1. 如果其他线程已经获取了写锁，则当前线程获取读锁失败，进入等待状态
2. 如果当前线程获取了写锁或者写锁未被获取，则当前线程（线程安全，依靠CAS保证）增加读状态，成功获取读锁

```java
protected final int tryAcquireShared(int unused) {
        for (;;) {
            int c = getState();
            int nextc = c + (1 << 16);
            if (nextc < c)
                throw new Error("Maximum lock count exceeded");
            //如果其他线程已经获取了写锁，则当前线程获取读锁失败，进入等待状态
            if (exclusiveCount(c) != 0 && owner != Thread.currentThread())
                return -1;
            //增加读状态，成功获取读锁
            if (compareAndSetState(c, nextc))
                return 1;
        }
    }
```

读锁释放，每次减少读状态(线程安全的，可能有多个读线程同时释放读锁)，减少的值是（1<<16）


## 锁降级

锁降级指的是写锁降级成为读锁,步骤为
1. 持有写锁(不释放,当前拥有的)
2. 获取读锁
3. 释放（先前拥有的）写锁

为什么要使用锁降级?从以下示例来解释,逻辑如下
1. 数据发生变更后，update变为false(布尔类型且volatile修饰)
2. 所有访问该方法的线程，感知变化，但只有一个线程能获取到写锁
3. 写锁修改完数据后，释放锁（注意在写锁是释放前，又获取了读锁）

```java
    public void processData() {
        //读锁会被写锁阻塞
        readLock.lock();
        if (!update) {
            // 必须先释放读锁
            readLock.unlock();
            // 锁降级从写锁获取到开始
            writeLock.lock();
            try {
                if (!update) {
            // 准备数据的流程（略）
                    update = true;
                }
                //再次获取读锁
                readLock.lock();
            } finally {
                writeLock.unlock();
            }
            // 锁降级完成，写锁降级为读锁
        }
        //step_join
        try {
            // 使用数据的流程（略）
        } finally {
            readLock.unlock();
        }
    }
```

为什么要再次获取读锁?
- 假设线程A释放写锁后不获取读锁
- 此时线程B在step_join 位置获取到了获取了写锁并修改了数据，但线程A无法获取到线程B更新的数据,继续使用数据就会不一致（注意此时setp_join位置线程A是没有任何锁的）
- 但如果加了读锁呢，线程A拥有读锁，线程B因为线程A持有读锁，导致其进入等待状态，直到线程A释放读锁，才能进行数据修改

问题：其实不用锁降级，单单依靠volatile和写锁，也能保证数据的安全性，那么为什么要用锁降级呢？

个人猜想：写锁会导致其他线程完全堵塞，而读锁是可以多线程访问的。比方说除了写锁锁定的代码外，其他代码是可以线程共享的，那么写锁降级读锁可以充分利用读锁的共享机制。

