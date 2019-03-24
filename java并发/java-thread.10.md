<!-- Java中的阻塞队列 -->

# 阻塞队列（BlockingQueue）

阻塞队列（BlockingQueue）是一个支持两个附加操作的队列。这两个附加的操作支持阻塞
的插入和移除方法。
1. 支持阻塞的插入方法：意思是当队列满时，队列会阻塞插入元素的线程，直到队列不满
2. 支持阻塞的移除方法：意思是在队列为空时，获取元素的线程会等待队列变为非空
3. 常用于生产者和消费者的场景

在阻塞队列不可用时，这两个附加操作提供了4种处理方式
1. 抛出异常：队列满，再往队列里插入元素，会抛出IllegalStateException（"Queuefull"）异常；队列空，当队列空时，从队列里获取元素会抛出NoSuchElementException异常
2. 返回特殊值：当往队列插入元素时，会返回元素是否插入成功，成功返回true。如果是移除方法，则是从队列里取出一个元素，如果没有则返回null
3. 一直阻塞：队列满，如果生产者线程往队列里put元素，队列会一直阻塞生产者线程，直到队列可用或者响应中断退出。当队列空时，如果消费者线程从队列里take元素，队列会阻塞住消费者线程，直到队列不为空
5. ·超时退出：队列满，如果生产者线程往队列里插入元素，队列会阻塞生产者线程一段时间，如果超过了指定的时间，生产者线程就会退出；队列空，抛出元素，队列会阻塞生产者线程一段时间，如果超过了指定的时间，生产者线程就会退出


![BlockingQueue](https://raw.githubusercontent.com/FameLsy/Images/master/thread/BlockingQueue.png)

>注意
>如果是无界阻塞队列，队列不可能会出现满的情况，所以使用put或offer方法永远不会被阻塞，而且使用offer方法时，该方法永远返回true。

# Java里的阻塞队列

JDK 7提供了7个阻塞队列
1. ArrayBlockingQueue：一个由数组结构组成的有界阻塞队列。
2. LinkedBlockingQueue：一个由链表结构组成的有界阻塞队列。
3. PriorityBlockingQueue：一个支持优先级排序的无界阻塞队列。
4. DelayQueue：一个使用优先级队列实现的无界阻塞队列。
5. SynchronousQueue：一个不存储元素的阻塞队列。
6. LinkedTransferQueue：一个由链表结构组成的无界阻塞队列。
7. LinkedBlockingDeque：一个由链表结构组成的双向阻塞队列。

## ArrayBlockingQueue
ArrayBlockingQueue:
- 由数组结构组成的有界阻塞队列
- 此队列按照先进先出（FIFO）的原则对元素进行排序
- 默认不保证线程公平的访问队列

创建一个公平的阻塞队列(即先阻塞先访问)
```java
ArrayBlockingQueue fairQueue = new ArrayBlockingQueue(1000,true);
```
访问者的公平性是使用可重入锁实现的
```java
public ArrayBlockingQueue(int capacity, boolean fair) {
    if (capacity <= 0)
        throw new IllegalArgumentException();
    this.items = new Object[capacity];
    lock = new ReentrantLock(fair);
    notEmpty = lock.newCondition();
    notFull = lock.newCondition();
}
```

## LinkedBlockingQueue

LinkedBlockingQueue
- 用链表实现的有界阻塞队列
- 默认和最大长度为Integer.MAX_VALUE
- FIFO

## PriorityBlockingQueue

 PriorityBlockingQueue
- 支持优先级的无界阻塞队列
- 默认情况下元素采取自然顺序升序排列
- 可以自定义类实现compareTo()方法来指定元素排序规则;也可以初始化PriorityBlockingQueue时，指定构造参数Comparator来对元素进行排序
- 但不能保证同优先级元素的顺序

## DelayQueue

DelayQueue
- 支持延时获取元素的无界阻塞队列
- 使用PriorityQueue来实现，
- 队列中的元素必须实现Delayed接口
- 在创建元素时可以指定多久才能从队列中获取当前元素。只有在延迟期满时才能从队列中提取元素。

应用场景
- 缓存系统的设计：可以用DelayQueue保存缓存元素的有效期，使用一个线程循环查询DelayQueue，一旦能从DelayQueue中获取元素时，表示缓存有效期到了。
- 定时任务调度：使用DelayQueue保存当天将会执行的任务和执行时间，一旦从DelayQueue中获取到任务就开始执行，比如TimerQueue就是使用DelayQueue实现的。

### 实现Delayed接口

步骤如下
1. 在对象创建的时候，初始化基本数据

```java
private static final AtomicLong sequencer = new AtomicLong(0);
ScheduledFutureTask(Runnable r, V result, long ns, long period) {
    super(r, result);
    this.time = ns;//time记录当前对象延迟到什么时候可以使用
    this.period = period;
    this.sequenceNumber = sequencer.getAndIncrement();//sequenceNumber来标识元素在队列中的先后顺序
}
```

2. 实现getDelay方法

```java
//该方法返回当前元素还需要延时多长时间，单位是纳秒(注意当time小于当前时间时，getDelay会返回负数)
public long getDelay(TimeUnit unit) {
    return unit.convert(time - now(), TimeUnit.NANOSECONDS);
}
```

3. 实现compareTo方法来指定元素的顺序

```java
public int compareTo(Delayed other) {
        if (other == this)　　// compare zero ONLY if same object
        return 0;
        if (other instanceof ScheduledFutureTask) {
            ScheduledFutureTask<> x = (ScheduledFutureTask<>)other;
            long diff = time - x.time;
            if (diff < 0)
                return -1;
            else if (diff > 0)
                return 1;
            else if (sequenceNumber < x.sequenceNumber)
                return -1;
            else
                return 1;
        }
        long d = (getDelay(TimeUnit.NANOSECONDS) -
                other.getDelay(TimeUnit.NANOSECONDS));
        return (d == 0) 0 : ((d < 0) -1 : 1);
    }
```

### 实现延时阻塞队列

```java
//从队列里获取元素时，如果元素没有达到延时时间，就阻塞当前线程

long delay = first.getDelay(TimeUnit.NANOSECONDS);
if (delay <= 0)
    return q.poll();
//leader：等待获取队列头部元素的线程
//如果leader不等于空，表示已经有线程在等待获取队列的头元素,使用await()方法让当前线程等待信号
else if (leader != null)
    available.await();
else {
    //leader等于空，则把当前线程设置成leader，并使用awaitNanos()方法让当前线程等待接收信号或等待delay时间
    Thread thisThread = Thread.currentThread();
    leader = thisThread;
    try {
        available.awaitNanos(delay);
    } finally {
        if (leader == thisThread)
            leader = null;
    }
}
```

## SynchronousQueue

SynchronousQueue
- 不存储元素的阻塞队列
- 每一个put操作必须等待一个take操作，否则不能继续添加元素
- 支持公平访问队列,默认非公平访问
- 非常适合传递性场景
- 吞吐量高于LinkedBlockingQueue和ArrayBlockingQueue

创建公平性访问的SynchronousQueue
```java
//fair == true -> 公平访问
public SynchronousQueue(boolean fair) {
    transferer = fair ? new TransferQueue() : new TransferStack();
}
```

## LinkedTransferQueue

LinkedTransferQueue
- 由链表结构组成的无界阻塞TransferQueue队列
- 相对于其他阻塞队列，LinkedTransferQueue多了tryTransfer和transfer方法。

### transfer方法

transfer方法
- 如果当前有消费者正在等待接收元素（消费者使用take()方法或带时间限制的poll()方法时），transfer方法可以把生产者传入的元素立刻transfer（传输）给消费者;。如果没有消费者在等待接收元素，transfer方法会将元素存放在队列的tail节点，并等到该元素被消费者消费了才返回

关键代码
```java
//尝试把存放当前元素的s节点作为tail节点
Node pred = tryAppend(s, haveData);
//让CPU自旋等待消费者消费元素（自旋会消耗CPU，所以自旋一定的次数后使用Thread.yield()方法来暂停当前正在执行的线程，并执行其他线程。）
return awaitMatch(s, pred, e, (how == TIMED), nanos);
```

### tryTransfer方法

用来试探生产者传入的元素是否能直接传给消费者
- 如果没有消费者等待接收元素，则返回false
- 如果消费了元素，则返回true
- 该方法无论消费者是否接收，方法立即返回；，而transfer方法是必须等到消费者消费了才返回

此外还有带有时间限制的tryTransfer（E e，long timeout，TimeUnit unit）方法
- 试图把生产者传入的元素直接传给消费者
- 如果没有消费者消费该元素则等待指定的时间再返回，如果超时还没消费元素，则返回false
- 如果在超时时间内消费了元素，则返回true。

### LinkedBlockingDeque

LinkedBlockingDeque
- 由链表结构组成的双向阻塞队列
- 因为多了一个操作队列的入口，在多线程同时入队时，也就减少了一半的竞争
- 相比其他的阻塞队列，LinkedBlockingDeque多了addFirst、addLast、offerFirst、offerLast、peekFirst和peekLast等方法
- 在初始化LinkedBlockingDeque时可以设置容量防止其过度膨胀。
- 双向阻塞队列可以  运用在“工作窃取”模式中。