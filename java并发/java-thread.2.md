

<!-- # 队列同步器 -->

# 队列同步器(AbstractQueuedSynchronizer)

同步器设计是基于模板方法模式，用来构建锁或者其他同步组件的基础框架，它使用了一个int成员变量表示同步状态，通过内置的FIFO队列来完成资源获取线程的排队工作

主要提供三个方法来改变或访问同步状态
```java
//获取当前同步状态。
getState()
//设置当前同步状态。
setState(int newState)：
//使用CAS设置当前状态，该方法能够保证状态设置的原子性。传递当前线程“认为”的尾节点和当前节点，如果设置成功（即tail成功指向当前节点）,则返回true
compareAndSetState(int expect,int update)：
```

## 同步器可重写的方法
![thread6](https://raw.githubusercontent.com/FameLsy/Images/master/thread/thread6.png)

## 同步器的模板方法

实现自定义同步组件时，将会调用同步器提供的模板方法，同步器提供的模板方法如下,其大致分为三类
1. 独占式获取与释放同步状态
2. 共享式获取与释放同步状态
3. 查询同步队列中的等待线程情况

![thread7](https://raw.githubusercontent.com/FameLsy/Images/master/thread/thread7.png)

## 队列同步器的实现分析

实现一个同步器，主要包括
1. 同步队列
2. 独占式同步状态获取与释放
3. 共享式同步状态获取与释放
4. 超时获取同步状态等同步器的核心数据结构与模板方法。

### 同步队列

使用FIFO双向队列完成同步状态的管理。
- 当前线程获取同步状态失败时，将节点加入该队列的尾部，同时会阻塞当前线程
- 当同步状态释放时，会把首节点中的线程唤醒，使其再次尝试获取同步状态。
- 同步队列中的节点（Node）用来保存获取同步状态失败的线程引用、等待状态以及前驱和后继节点，节点的属性类型与名称以及描述

节点描述：

![thread8](https://raw.githubusercontent.com/FameLsy/Images/master/thread/thread8.png)


此外，同步器拥有首节点（head）和尾节点（tail），以下是同步器的基本结构

![thread9](https://raw.githubusercontent.com/FameLsy/Images/master/thread/thread9.png)

对于入列操作，需要保证其安全性，同步器提供了一个基于CAS的设置尾节点的方法
```java
compareAndSetTail(Node expect,Node update)
```

对于获取首节点，由于只有一个线程能够成功获取到同步状态，因此设置头节点的方法并不需要使用CAS来保证

### 独占式同步状态获取与释放
独占式同步状态获取流程

![thread10](https://raw.githubusercontent.com/FameLsy/Images/master/thread/thread10.png)



同步器使用acquire()方法独占式获取同步状态，其逻辑步骤如下
1. 调用*自定义同步器*实现的tryAcquire(int arg)方法,该方法保证线程安全的获取同步状态
2. 如果同步状态获取失败，则构造同步节点(Node.EXCLUSIVE),并使用addWaiter(Node node)加入到队列尾部，然后调用acquireQueued(Node node,int arg)方法，使得该节点以“死循环”的方式获取同步状态
3. 以上两步均没有获取到同步状态，则阻塞节点中的线程，selfInterrupt();

```java
public final void acquire(int arg) {
if (!tryAcquire(arg) && acquireQueued(addWaiter(Node.EXCLUSIVE), arg)){
        selfInterrupt();
    }
}
```

同步器的addWaiter和enq方法
```java
private Node addWaiter(Node mode) {
        //创建节点
        Node node = new Node(Thread.currentThread(), mode);
        //尾节点
        Node pred = tail;
        //尝试快速添加，可能由于多个请求并发进行而添加失败
        if (pred != null) {
            //尾节点设置为新节点的前驱节点
            node.prev = pred;
            //使用compareAndSetTail,确保节点能够被线程安全添加列尾
            if (compareAndSetTail(pred, node)) {
                pred.next = node;
                return node;
            }
        }
        //快速添加失败，进入死循环添加，将并发添加节点的请求通过CAS变得“串行化”
        enq(node);
        return node;
    } 
    //通过“死循环”来保证节点的正确添加，CAS
    private Node enq(final Node node) {
        for (;;) {
            Node t = tail;
            if (t == null) { // Must initialize
                if (compareAndSetHead(new Node()))
                    tail = head;
            } else {
                node.prev = t;
                if (compareAndSetTail(t, node)) {
                    t.next = node;
                    return t;
                }
            }
        }
    }
```

同步器的acquireQueued方法

```java
//在“死循环”中尝试获取同步状态，而只有 前驱节点 是头节点才能够尝试获取同步状态
    final boolean acquireQueued(final Node node, int arg) {
        boolean failed = true;
        try {
            boolean interrupted = false;
            for (;;) {
                final Node p = node.predecessor();
                if (p == head && tryAcquire(arg)) {
                    setHead(node);
                    p.next = null; // help GC
                    failed = false;
                    return interrupted;
                }
                if (shouldParkAfterFailedAcquire(p, node) &&
                        parkAndCheckInterrupt())
                    interrupted = true;
            }
        } finally {
            if (failed)
                cancelAcquire(node);
        }
    }
```

为什么只有前驱节点 是头节点才能够尝试获取同步状态？原因如下
1. 头节点是成功获取到同步状态的节点，而头节点的线程释放了同步状态之后，将会唤醒其后继节点，后继节点的线程被唤醒后需要检查自己的前驱节点是否是头节点
2. 维护同步队列的FIFO原则

当同步状态获取成功之后，当前线程从acquire(int arg)方法返回，如果对于锁这种并发组件而言，代表着当前线程获取了锁

当前线程获取同步状态并执行了相应逻辑之后，就需要释放同步状态，使得后续节点能够继续获取同步状态。通过调用同步器的release(int arg)方法可以释放同步状态，该方法在释放了同步状态之后，会唤醒其后继节点（进而使后继节点重新尝试获取同步状态）

```java
    public final boolean release(int arg) {
        if (tryRelease(arg)) {
            Node h = head;
            //unparkSuccessor()使用LockSupport来唤醒处于等待状态的线程
            if (h != null && h.waitStatus != 0)
                unparkSuccessor(h);
            return true;
        }
        return false;
    }
```

## 共享式同步状态获取与释放

与独占式的区别在于同一时刻能否有多个线程同时获取到同步状态，如对于同一份文件的读操作，对于共享式的访问均被允许，而独占式只能有一个被访问，其他的独占式访问均被拒绝

同步器使用acquireShared(int arg)方法共享式获取同步状态，其逻辑步骤如下
1. 调用tryAcquireShared(int arg)方法尝试获取同步状态，返回int,大于等于0时，表示能够获取到同步状态,否则进入doAcquireShared()方法
2. 在doAcquireShared()方法中，如果当前节点的前驱为头节点时，尝试获取同步状态，如果返回值大于等于0，表示该次获取同步状态成功并从自旋过程中退出



```java
public final void acquireShared(int arg) {
        if (tryAcquireShared(arg) < 0)
            doAcquireShared(arg);
    } 

private void doAcquireShared(int arg) {
    final Node node = addWaiter(Node.SHARED);
    boolean failed = true;
    try {
        boolean interrupted = false;
        for (;;) {
            final Node p = node.predecessor();
            if (p == head) {
                int r = tryAcquireShared(arg);
                if (r >= 0) {
                    setHeadAndPropagate(node, r);
                    p.next = null;
                    if (interrupted)
                        selfInterrupt();
                    failed = false;
                    return;
                }
            }
            if (shouldParkAfterFailedAcquire(p, node) &&
                    parkAndCheckInterrupt())
                interrupted = true;
        }
    } finally {
        if (failed)
            cancelAcquire(node);
    }
}

```

通过调用releaseShared(int arg)方法释放同步状态
```java
public final boolean releaseShared(int arg) {
    //tryReleaseShared:必须确保同步状态（或者资源数）线程安全释放，一般是通过循环和CAS来保证,，因为释放同步状态的操作会同时来自多个线程。(独占式则不需要，因为是一个线程释放)
    if (tryReleaseShared(arg)) {
        doReleaseShared();
        return true;
    }
    return false;
}
```

### 独占式超时获取同步状态

独占式超时同步状态获取流程

![thread11](https://raw.githubusercontent.com/FameLsy/Images/master/thread/thread11.png)


同步器通过调用doAcquireNanos(int arg,long nanosTimeout)方法可以超时获取同步状态，即在指定的时间段内获取同步状态，其逻辑如下
1. 主要需要计算出需要睡眠的时间间隔nanosTimeout，公式为：nanosTimeout-=now-lastTime（其中now为当前唤醒时间，lastTime为上次唤醒时间）
2. 如果nanosTimeout大于0则表示超时时间未到，需要重新计算超时间隔nanosTimeout，继续睡眠nanosTimeout纳秒，反之，表示已经超时

```java
//当前唤醒时间
long now = System.nanoTime();
private boolean doAcquireNanos(int arg, long nanosTimeout)
            throws InterruptedException {
        //上次唤醒时间
        long lastTime = System.nanoTime();
        final Node node = addWaiter(Node.EXCLUSIVE);
        boolean failed = true;
        try {
            for (;;) {
                final Node p = node.predecessor();
                if (p == head && tryAcquire(arg)) {
                    setHead(node);
                    p.next = null; // help GC
                    failed = false;
                    return true;
                }
                //-----------------重点-----------------------------
                if (nanosTimeout <= 0)
                    return false;
                if (shouldParkAfterFailedAcquire(p, node)
                        && nanosTimeout > spinForTimeoutThreshold)
                    LockSupport.parkNanos(this, nanosTimeout);
                long now = System.nanoTime();
                //计算时间，当前时间now减去睡眠之前的时间lastTime得到已经睡眠的时间delta，然后被原有超时时间nanosTimeout减去，得到了还应该睡眠的时间
                nanosTimeout -= now - lastTime;
                lastTime = now;
                if (Thread.interrupted())
                    throw new InterruptedException();
            }
        //-----------------------------------------------------------

        } finally {
            if (failed)
                cancelAcquire(node);
        }
    }
```

>注意  
>如果nanosTimeout小于等于spinForTimeoutThreshold（1000纳秒）时，将不会使该线程进行超时等待，而是进入快速的自旋过程(非常短的超时等待无法做到十分精确)


