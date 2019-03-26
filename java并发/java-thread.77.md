<!-- 　Condition接口 -->

任意一个Java对象，都拥有一组监视器方法（定义在java.lang.Object上），主要包括wait()、wait(long timeout)、notify()以及notifyAll()方法，这些方法与synchronized同步关键字配合，可以实现等待/通知模式；而Condition接口也提供了类似Object的监视器方法，与Lock配合可以实现等待/通知模式

condition对比Object

![condition](https://raw.githubusercontent.com/FameLsy/Images/master/thread/condition.png)

# Condition接口

Condition定义了等待/通知两种类型的方法，当前线程调用这些方法时，需要提前获取到Condition对象关联的锁,Condition依赖Lock对象

```java
Lock lock = new ReentrantLock();
// 通过lock.newCondition()来获取Condition对线
Condition condition = lock.newCondition();

//线程1
public void conditionWait() throws InterruptedException {
    lock.lock();
    try {
        //condition.await();当前线程会释放锁并在此等待
        condition.await();
    } finally {
        lock.unlock();
    }
} 

//线程2
public void conditionSignal() throws InterruptedException {
    lock.lock();
    try {
        //通知当前线程从await()方法返回（当然需要拿到锁）
        condition.signal();
    } finally {
        lock.unlock();
    }
}
```

Condition的（部分）方法以及描述

![condition2](https://raw.githubusercontent.com/FameLsy/Images/master/thread/condition2.png)

# Condition的实现分析

ConditionObject是同步器AbstractQueuedSynchronizer的内部类,每个Condition对象都包含着一个队列（以下称为等待队列），该队列是Condition对象实现等待/通知功能的关键。

Condition的实现，主要包括：
1. 等待队列
2. 等待
3. 通知

## 等待队列

等待队列
- FIFO的队列
- 节点包含了一个线程引用（在Condition对象上等待的线程），复用了同步器中节点的定义
- 拥有首节点（firstWaiter）和尾节点（lastWaiter）
- 节点更新无需CAS,因为await()方法必然是获取了锁的线程

同步器拥有一个同步队列和多个等待队列

![condition3](https://raw.githubusercontent.com/FameLsy/Images/master/thread/condition3.png)

## 等待

调用了Condition.await()方法，该线程将会释放锁、构造成节点加入等待队列并进入等待状态

逻辑如下
1. 当前线程必定是获取到锁的线程,即同步队列的首节点
2. 该方法会将当前线程构造成节点并加入等待队列中，然后释放同步状态,唤醒后继节点
3. 当前线程进入等待状态
4. 相当于将同步队列的首节点移动到了等待队列中(通过addConditionWaiter,把当前线程构造成一个新的节点加入到等待队列，而不是真正意义上的移动)

```java
    public final void await() throws InterruptedException {
        if (Thread.interrupted())
            throw new InterruptedException();
        // 当前线程加入等待队列
        Node node = addConditionWaiter();
        // 释放同步状态，也就是释放锁
        int savedState = fullyRelease(node);
        int interruptMode = 0;
        // 被唤醒的程序将从循环中退出，isOnSyncQueue(node)判断当前节点是否已经在同步队列
        while (!isOnSyncQueue(node)) {
            LockSupport.park(this);
            if ((interruptMode = checkInterruptWhileWaiting(node)) != 0)
                break;
        }
        //调用同步器的acquireQueued()方法加入到获取同步状态的竞争中
        if (acquireQueued(node, savedState) && interruptMode != THROW_IE)
            interruptMode = REINTERRUPT;
        if (node.nextWaiter != null)
            unlinkCancelledWaiters();
        if (interruptMode != 0)
            reportInterruptAfterWait(interruptMode);
    }
```

## 通知
调用Condition的signal()方法,将会唤醒在等待队列中等待时间最长的节点（首节点），在唤醒节点之前，会将节点移到同步队列中

逻辑如下
1. 当前线程必须获取了锁（通过isHeldExclusively()检查）
2. 移动等待队列首节点到同步队列并使用LockSupport唤醒节点中的线程

```java
public final void signal() {
    // 判断当前线程获取到了锁
    if (!isHeldExclusively())
        throw new IllegalMonitorStateException();
    // 获取等待队列的首节点
    Node first = firstWaiter;
    if (first != null)
    // 移动到同步队列并使用LockSupport唤醒节点中的线程
        doSignal(first);
}
```
![condition4](https://raw.githubusercontent.com/FameLsy/Images/master/thread/condition4.png)

Condition的signalAll()方法，相当于对等待队列中的每个节点均执行一次signal()方法，效果就是将等待队列中所有节点全部移动到同步队列中，并唤醒每个节点的线程。