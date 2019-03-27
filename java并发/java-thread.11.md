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

