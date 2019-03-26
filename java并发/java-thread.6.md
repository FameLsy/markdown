<!-- 锁对象和条件对象 -->

为了实现对 *代码块* 并发访问的防干扰，java提供了两种机制
1. synchronized 关键字
2. 显示锁的使用

在此之前，先理解锁对象和条件对象

# 锁对象

如下代码，一旦一个线程封锁了锁对象，其他任何线程都无法通过lock()语句，会被阻塞，直到第一个线程释放锁对象
```java
myLock.lock();
try{
    do something
}
finally{
    myLock.unlock();
}
```

# 条件对象

条件对象用于管理那些获得锁但无法执行有用工作的线程，当条件不满足时，先释放掉锁，等到条件满足在进行操作。一个锁对象，可以对应对各条件对象

```java
//ThreadA
myLcok.lock();//锁定
try{
    //获取一个条件对象
    aCondtion = myLock.newCondition();

    //当条件不满足时，通过条件对象让线程阻塞，并释放锁
    //该线程很一直处于阻塞状态，直到其他线程aCondition.signal()，唤醒等待的线程
    //被唤醒后如果条件满足，则可以退出循环，继续操作了
    while(!(ok to proceed)){
        aCondition.await();
    }
}
```

条件对象需要设置几个方法
```java
//让线程阻塞并释放锁
await();
//唤醒单个被await()阻塞的队列
signal();
//唤醒所有被await()阻塞的队列
signalAll()
```

在Object中，已经定义了三个类似的方法
```java
//让线程阻塞并释放锁
wait();
//唤醒单个被await()阻塞的队列
notify();
//唤醒所有被await()阻塞的队列
notifyAll();
```

Condition和Object的三个方法使用了不同的方法名，这样可以避免方法之间的冲突