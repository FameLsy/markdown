

<!-- # 队列同步器 -->

# 队列同步器(AbstractQueuedSynchronizer)

简称AQS，是一个抽象类，用来构建锁或者其他同步组件的基础框架，同步器设计是基于模板方法模式，它使用了一个int成员变量表示同步状态，通过内置的FIFO队列来完成资源获取线程的排队工作。子类通过继承他，来进行管理同步状态，AQS主要提供三个方法来改变或访问同步状态

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

# 队列同步器的实现分析

实现一个同步器，主要包括
1. 同步队列
2. 提供独占式同步状态获取与释放方法
3. 提供共享式同步状态获取与释放方法
4. 提供超时获取同步状态等同步器的核心数据结构与模板方法。

```java
//--------------同步队列结构---------------------------
//头节点
    private transient volatile Node head;
//尾节点
    private transient volatile Node tail;
//同步状态
    private volatile int state;
//一个Node节点类
    static final class Node{...}

//--------------独占式同步状态获取与释放方法---------------------------
public final void acquire(int arg){...}

//--------------共享式同步状态获取与释放方法---------------------------
public final void acquireShared(int arg){...}

```

当我们调用锁的lock()和unlock()方法时，实际上操作的就是同步器获取和释放同步状态


## 同步队列

同步队列是为了将没有获取同步状态的线程先保存，然后通过FIFO双向队列完成 *同步状态* 的管理,其处理逻辑如下
- 线程A和线程B同时获取同步状态，A获取成功，相当于获取到了锁；B获取失败后进入线程中断，将B打包成节点放入到队列尾部
- 线程A执行完毕，释放同步状态（释放锁），因为FIFO的原则，获取到队列的首节点，从节点获取到相应的线程信息，唤醒该线程，线程被唤醒后，再次尝试获取同步状态；

节点属性描述：

![thread8](https://raw.githubusercontent.com/FameLsy/Images/master/thread/thread8.png)


此外，同步器拥有首节点（head）和尾节点（tail），以下是同步器的基本结构

![thread9](https://raw.githubusercontent.com/FameLsy/Images/master/thread/thread9.png)

### 入队操作
对于入列操作，因为可能又多个线程同时操作入列，所以需要保证其安全性，同步器提供了一个基于CAS的设置尾节点的方法
```java
//该方法时调用了Unsafe类的方法，Unsafe可以保证原子操作
compareAndSetTail(Node expect,Node update){
    //this为需要更新的对象
    //tailOffset:对象中变量的偏移量，可以理解为尾节点的引用地址
    //expect:期望的值
    //updaet: 更新值
    //如果expect的值与update值一样，则更新tailOffset;相当于将tail指向update节点
    return unsafe.compareAndSwapObject(this, tailOffset, expect, update);

}
```
入队的完整操作需要实现三个操作
1. node.prev指向原tail节点
2. tail节点指向node
3. 原tail节点的后继节点指向node

```java
//node为需要入列的节点
//先将node的前驱节点设置为tail
node.prev = t;
//然后通过同步器提供的CAS设置尾节点方式，将tail节点指向node节点
//如果成功，那么再将原tail节点的后继结点指向node
if (compareAndSetTail(t, node)) {
    t.next = node;
    return t;
}
```
### 出队操作

对于获取首节点而言，并不需要使用CAS来保证，只需要正常的出队列即可

## 独占式同步状态获取与释放



独占式同步状态获取释放流程

![thread10](https://raw.githubusercontent.com/FameLsy/Images/master/thread/thread10.png)

### 获取流程



源码:AQS通过模板方法acquire()方法来进行独占式的同步状态获取，获取成功，则由该方法返回；失败，则进入等待队列

```java
//AQS源码
    public final void acquire(int arg) {
        if (!tryAcquire(arg) &&
            acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
            selfInterrupt();
    }
```

对于tryAcquire(int arg)方法，它是同步器自定义的获取同步状态的方法,如果不重写，原方法只是抛出一个UnsupportedOperationException
```java
//AQS中的tryAcquire
   protected boolean tryAcquire(int arg) {
        throw new UnsupportedOperationException();
    }
```

对于addWaiter()方法,它会构造一个节点并加入队列列尾，在AQS，构造的是Node.EXCLUSIVE的节点；他会现场时快速添加一个节点，如果失败，在进入enq(node),进入死循环中进行CAS添加
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

对于acquireQueued()方法,可以看到，它会以死循环的方式从头节点开始获取同步状态

```java
//AQS
//在“死循环”中尝试获取同步状态，而只有 前驱节点 是头节点才能够尝试获取同步状态
    final boolean acquireQueued(final Node node, int arg) {
        boolean failed = true;
        try {
            //设置中断标记
            boolean interrupted = false;
            for (;;) {
                //获取前驱节点
                final Node p = node.predecessor();
                //
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

对于selfInterrupt()，它是当两种方式获取状态都失败后，让线程中断等待唤醒
```java
static void selfInterrupt() {
        Thread.currentThread().interrupt();
    }
```


### 释放流程

当前线程获取同步状态并执行了相应逻辑之后，就需要释放同步状态，唤醒其后继节点能够继续获取同步状态。

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

与独占式的区别在于:同一时刻能否有多个线程同时获取到同步状态，如对于同一份文件的读操作，对于共享式的访问均被允许，而独占式只能有一个被访问，其他的独占式访问均被拒绝


### 获取流程

```java
//tryAcquireShared(int arg)方法会返回一个int，如果int大于0，说明能够获取同步状态
public final void acquireShared(int arg) {
        if (tryAcquireShared(arg) < 0)
            doAcquireShared(arg);
    } 
//doAcquireShared()在当前节点的前驱节点为头节点时，会尝试获取同步状态，如果返回值大于等于0，表示获取成功
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
### 释放流程

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


