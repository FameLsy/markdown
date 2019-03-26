<!-- # 线程状态 -->

![thread1](https://raw.githubusercontent.com/FameLsy/Images/master/thread/thread1.png)

获取线程状态
```java
//java 5新增
public State getState();
```

Thread在内部定义了一个枚举类
```java
public enum State {

    NEW,

    RUNNABLE,

    BLOCKED,

    WAITING,

    TIMED_WAITING,

    TERMINATED;
}
```

# NEW（新创建）状态

创建了线程但没有执行start()方法，此时处于新创建状态

# RUNNABLE (可运行) 状态

调用了start()方法的线程就处于RUNNABLE状态，需要注意的是，该状态下的线程可能正在运行，也可能没有运行，所以是*可运行*状态

# BLOCKED （阻塞）状态

当系统试图获取一个内部的对象锁（注意，不是java.util.concurrent库中的锁），但该锁已被其他锁持有，当前线程就会进入阻塞状态

# WAITING (等待)状态

调用Object.wait方法、Thread.join方法，或者等待java.util.concurrent库中的Lock或Condition时,会进入等待状态

# TIME_WAITING (计时等待)状态

线程因调用了某些带超时参数的方法时，会计入该状态。该状态会持续到超时期满或者接收到适当的通知

# TERMINATER (终止)状态

线程终止时的状态

# 线程状态的变迁

![thread2](https://raw.githubusercontent.com/FameLsy/Images/master/thread/thread2.png)


>注意  
>Java将操作系统中的运行和就绪两个状态合并称为运行状态。阻塞状态是线程阻塞在进入synchronized关键字修饰的方法或代码块（获取锁）时的状态，但是阻塞在java.concurrent包中Lock接口的线程状态却是等待状态，因为java.concurrent包中Lock接口对于阻塞的实现均使用了LockSupport类中的相关方法

