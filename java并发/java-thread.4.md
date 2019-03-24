
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
//区别在于判断条件多了hasQueuedPredecessors(),判断是否有前驱节点，如果有，则返回true
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
```

非公平性锁的一个线程连续获取锁的情况  
- 原因为刚释放锁的线程再次获取同步状态的几率会非常大，使得其他线程只能在同步队列中等待,可能造成线程饥饿（使得其他线程只能在同步队列中等待）

为什么非公平锁为默认？
- 公平锁锁保证了锁的获取按照FIFO原则，而代价是进行大量的线程切换。非公平性锁虽然可能造成线程“饥饿”，但极少的线程切换，保证了其更大的吞吐量。
