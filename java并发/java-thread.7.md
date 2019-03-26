<!-- synchronized关键字 -->

# synchronized关键字

java从1.0开始，每个对象都有一个内部的对象锁，关键字synchronized可以修饰方法或者方法块所使用的就是该对象的内部对象锁和相关的条件

如下函数
```java
public synchronized void method(){
    do something;
}
```
可以把它看成
```java
public void method(){
    //this.intrinsicLock相当于内部对象锁
    this.intrinsicLock.lock();
    try{
        do something;
    }finally{
        this.intrinsicLock.unlock();
    }
}
```

不同的是，内部对象只有一个条件对象(毕竟，不能显式的newCondition来获取条件对象)
```java
this.intrinsiCondition.await();
this.intrinsiCondition.signal();
this.intrinsiCondition.signalAll();
```

事实上，synchronized关键字锁的是this,可以通过如下代码来验证
```java
//这种synchronized(obj)的方式通过一个对象来实现额外的原子操作，被称为客户端锁定，但并不推荐使用
synchronized(this){
    ...
}
```

此外，可以通过如下代码来验证关键字也可以使用在静态方法上，此时锁的是对象的class文件,可以通过如下代码来验证
```java
synchronized(aObject.class){
    ...
}
```

synchronized带来便捷的同时，也存在了一些局限性
1. 不能中断一个试图获取锁的程序
2. 试图获取锁不能设置超时
3. 只能单一条件


---

# JVM中的synchronized

对于同步块的实现使用了monitorenter和monitorexit指令；同步方法则是依靠方法修饰符上的ACC_SYNCHRONIZED。本质是对一个对象的监视器（monitor）进行获取，而这个获取过程是排他的。

监视器
1. 任意一个对象都拥有自己的监视器
2. 同步块或同步方法执行时必须获取该对象的监视器，才能进入；
3. 没有获取到监视的线程将会被阻塞在同步块和同步方法的入口处，线程进入同步队列，状态变为BLOCKED状态。

>注：  
>可见性：一个线程对共享变量值的修改，能够及实地被其他线程看到  
>排他性：即线程对块或方法的独立占有　