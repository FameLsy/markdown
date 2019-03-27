<!-- 　LockSupport工具 -->

当需要阻塞或唤醒一个线程的时候，都会使用LockSupport工具类来完成相应工作;LockSupport定义了一组静态方法，以park开头的方法用来阻塞当前线程，以及unpark(Thread thread)方法来唤醒一个被阻塞的线程

LockSupport提供的阻塞和唤醒方法

![locksupport](https://raw.githubusercontent.com/FameLsy/Images/master/thread/locksupport.png)


java6 添加
1. park(Object blocker)
2. parkNanos(Object blocker,long nanos)
3. parkUn   til(Object blocker,long deadline)
4. 用于实现阻塞当前线程的功能,blocker是用来标识当前线程在等待的对象(阻塞对象),该对象主要用于问题排查和系统监控(能传递更多的信息)，可以通过dump 线程来对比
