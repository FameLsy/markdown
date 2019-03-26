<!-- Java中的线程池 -->

线程池能够带来3个好处
1. 降低资源消耗
2. 提高响应速度
3. 提高线程的可管理性



# 线程池的初始化

ThreadPoolExecutor是Java提供的一个核心的线程池,通过它来创建一个线程池
```java
new ThreadPoolExecutor(     int corePoolSize,
                            int maximumPoolSize,
                            long keepAliveTime,
                            TimeUnit unit,
                            BlockingQueue<Runnable> workQueue,
                            ThreadFactory threadFactory,
                            RejectedExecutionHandler handler)
```

其参数定义如下

corePoolSize
- 线程池的基本大小
- 任务数在小于它时每提交一个任务就会创建一个新的线程（无论其他线程是否空闲）
- 当任务数大于它时，不会再创建线程
- 可以使用prestartAllCoreThreads()方法提前创建并开启所有基本线程

maximumPoolSize
- 线程池最大数量
- 如果队列满了，并且已创建的线程数小于最大线程数，则线程池会再创建新的线程执行任务
- 使用了无界的任务队列此参数失效

keepAliveTime
- 线程活动保持时间
- 线程池的工作线程空闲后，保持存活的时间
- 任务多时且执行时间短，可以调大它提高线程利用率


TimeUnit
- 线程活动保持时间的单位
- 可选单位有天（DAYS）、小时（HOURS）、分钟（MINUTES）、毫秒（MILLISECONDS）、微秒（MICROSECONDS，千分之一毫秒）和纳秒（NANOSECONDS，千分之一微秒）


BlockingQueue
- 任务队列
- 用于保存等待执行的任务的阻塞队列
- 可以选择阻塞队列的有：ArrayBlockingQueue、SynchronousQueue、LinkedBlockingQueue、PriorityBlockingQueue


ThreadFactory
- 创建线程的工厂
- 可以通过线程工厂给每个创建出来的线程设  置更有意义的名字
- (使用开源框架guava提供的ThreadFactoryBuilder可以快速给线程池里的线程设置有意义的名字)

RejectedExecutionHandler
- 饱和策略
- 在队列和线程池都满情况下使用，可选策略有
- AbortPolicy，表示无法处理新任务时抛出异常,默认
- CallerRunsPolicy：只用调用者所在线程来运行任务
- DiscardOldestPolicy：丢弃队列里最近的一个任务，并执行当前任务
- DiscardPolicy：不处理，丢弃掉
- 也可以根据应用场景需要来实现RejectedExecutionHandler接口自定义策略

# 向线程池提交任务

可以使用两个方法向线程池提交任务
1. execute()：提交不需要返回值的任务，所以无法判断任务是否被线程池执行成功
2. submit()：提交需要返回值的任务，线程池会返回一个Future类型的对象，

Future：
- 通过该对象可以判断任务是否执行成功
- 可以通过future的get()方法来获取返回值，get()方法造成阻塞
- 可以使用超时方法get（long timeout，TimeUnit unit）

# 关闭线程池

可以通过调用线程池提供的两个方法来关闭线程池
1. shutdown：只是将线程池的状态设置成SHUTDOWN状态，然后中断所有没有正在执行任务的线程（通常使用）
2. shutdownNow：将线程池的状态设置成STOP，然后尝试停止所有的正在执行或暂停任务的线程，并返回等待执行任务的列表（如果任务无需执行完成，则可以使用）
3. 原理是遍历线程池中的工作线程，然后逐个调用线程的interrupt方法来中断线程(无法响应中断的任务可能永远无法终止)

判断是否关闭
```java 
//执行shutdown()和shutdownNow后就会返回true
aThreadPoolExecutor.isShutdown()
//执行shutdown()和shutdownNow后,且任务全部关闭才返回true
aThreadPoolExecutor.isTerminaed()
```

# 合理地配置线程池

要想合理地配置线程池，就必须首先分析任务特性
1. 任务的性质：CPU密集型任务、IO密集型任务和混合型任务。
2. 任务的优先级：高、中和低。
3. 任务的执行时间：长、中和短。
4. 任务的依赖性：是否依赖其他系统资源，如数据库连接

建议使用有界队列

# 线程池的监控

监控线程池的时候可以使用以下属性
1. taskCount：线程池需要执行的任务数量。
2. completedTaskCount：线程池在运行过程中已完成的任务数量，小于或等于taskCount。
3. largestPoolSize：线程池里曾经创建过的最大线程数量。通过这个数据可以知道线程池是否曾经满过。如该数值等于线程池的最大大小，则表示线程池曾经满过。
4. getPoolSize：线程池的线程数量。如果线程池不销毁的话，线程池里的线程不会自动销毁，所以这个大小只增不减。
5. getActiveCount：获取活动的线程数。

# 线程池的实现原理

线程池的处理逻辑
1. 判断核心线程池里的线程是否都在执行任务；否，则创建新的线程来执行任务；是，则进入2
2. 判断工作队列是否已经满；否，则将新提交的任务存储在这个工作队列里；是，则进入3
3. 判断线程池的线程是否都处于工作状态;否，则创建一个新的工作线程来执行任务；是，则交给饱和策略来处理这个任务

![xcc](https://raw.githubusercontent.com/FameLsy/Images/master/thread/xcc.png)

执行过程(ThreadPoolExecutor为例)
1. 判断：如果当前运行的线程数n与corePoolSize  
    如果n < corePoolSize 则创建新线程来执行任务（执行这一步骤需要获取全局锁）;结束；
    如果n >= corePoolSize,则将任务加入BlockingQueue,进行下一步判断
2. 判断：BlockingQueue是否已满  
    否则，则将任务加入队列中;结束；
    如果队列已满，则需要创建新的线程来处理任务（需要获取全局锁）;继续判断
3. 判断：创建新进程后进程数是否超出maximumPoolSize  
    是，任务将被拒绝，并调用RejectedExecutionHandler.rejectedExecution()方法
    否，则创建线程，结束


<!-- ![scc2](https://raw.githubusercontent.com/FameLsy/Images/master/thread/scc2.png) -->

```java
public void execute(Runnable command) {
    if (command == null)
        throw new NullPointerException();
    // 如果线程数小于基本线程数，则创建线程并执行当前任务
    if (poolSize >= corePoolSize || !addIfUnderCorePoolSize(command)) {
        // 如线程数大于等于基本线程数或线程创建失败，则将当前任务放到工作队列中。
        if (runState == RUNNING && workQueue.offer(command)) {
            if (runState != RUNNING || poolSize == 0)
                ensureQueuedTaskHandled(command);
        }
        else if (!addIfUnderMaximumPoolSize(command))
            // 抛出RejectedExecutionException异常
            reject(command); // is shutdown or saturated
    }
}
```

线程池创建线程时，会将线程封装成工作线程Worker，Worker在执行完任务后，还会循环获取工作队列里的任务来执行
```java
//Woker类的run方法
    public void run() {
        try {
            Runnable task = firstTask;
            firstTask = null;
            while (task != null || (task = getTask()) != null) {
                runTask(task);
                task = null;
            }
        } finally {
            workerDone(this);
        }
    }
```

线程池中的线程执行任务分两种情况
1. 在execute()方法中创建一个线程时，会让这个线程执行当前任务
2. 线程执行完任务后，会反复从BlockingQueue获取任务来执行。