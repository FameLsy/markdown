<!-- Executor框架 -->

在JDK 5之前，java线程既是工作单元，也是执行机制，JDK 5之后把工作单元与执行机制分离开来。工作单元包括Runnable和Callable，而执行机制由Executor框架提供,其内部使用了线程池机制,通过该框架来控制线程的启动、执行和关闭，可以简化并发编程的操作





# Executor框架的两级调度模型

在HotSpot VM的线程模型中，Java线程（java.lang.Thread）被一对一映射为本地操作系统线
程。

如图所示
- 上层：ava多线程程序通常把应用分解为若干个任务，然后使用用户级的调度器（Executor框架）将这些任务映射为固定数量的线程
- 底层：操作系统内核将这些线程映射到硬件处理器CPU上

![executor](https://raw.githubusercontent.com/FameLsy/Images/master/thread/executor.png)

# Executor框架的成员

## ThreadPoolExecutor

ThreadPoolExecutor：
- 线程池的核心实现类
- 用来执行被提交的任务。
- 通常使用工厂类Executors来创建

ThreadPoolExecutor主要由下列4个组件构成。
1. corePool：核心线程池的大小。
2. maximumPool：最大线程池的大小。
3. BlockingQueue：用来暂时保存任务的工作队列
4. RejectedExecutionHandler：当ThreadPoolExecutor已经关闭或ThreadPoolExecutor已经饱和时（达到了最大线程池大小且工作队列已满），execute()方法将要调用的Handler

Executors可以创建3种类型的ThreadPoolExecutor
1. FixedThreadPool：创建使用固定线程数的FixedThreadPool(适用于负载比较重的服务器)
2. SingleThreadExecutor：创建使用单个线程的SingleThreadExecutor（适用于需要保证顺序地执行各个任务；并且在任意时间点，不会有多个线程是活动的应用场景。）
3. CachedThreadPoo：创建一个会根据需要创建新线程的CachedThreadPool（大小无界线程池，适用于执行很多的短期异步任务的小程序，或者是负载较轻的服务器。）


### FixedThreadPool

```java
public static ExecutorService newFixedThreadPool(int nThreads) {
    return new ThreadPoolExecutor(
                nThreads, 
                nThreads,
                0L, //keepAliveTime设置为0L，意味着多余的空闲线程会被立即终止
                TimeUnit.MILLISECONDS, 
                new inkedBlockingQueue<Runnable>());
}
```
### SingleThreadExecutor

### CachedThreadPool

## ScheduledThreadPoolExecutor

ScheduledThreadPoolExecutor
- 线程池实现类
- 可以在给定的延迟后运行命令，或者定期执行命令
- 通常使用工厂类Executors来创建

Executors可以创建2种类型的ScheduledThreadPoolExecutor
1. ScheduledThreadPoolExecutor：，创建固定个数线程的ScheduledThreadPoolExecutor(适用于需要多个后台线程执行周期任务，同时为了满足资源管理的需求而需要限制后台线程的数量的应用场景)
2. SingleThreadScheduledExecutor：创建单个线程的ScheduledThreadPoolExecutor(适用于需要单个后台线程执行周期任务，同时需要保证顺序地执行各个任务的应用场景。)

## Future接口和实现Future接口的FutureTask类

Future接口和实现Future接口的FutureTask类
- 表示异步计算的结果
- 通过sumbit()提交给线程池后返回该类型的数据


## Runnable接口和Callable接口

Runnable接口和Callable接口
- 可以被ThreadPoolExecutor或Scheduled-ThreadPoolExecutor执行
- Runnable不会返回结果
- Callable可以返回结果
- 工具类Executors可以把一个Runnable对象封装为一个Callable对象

```java
//Runnable->Callable
// sumbit后返回null
Executors.callable（Runnable task）
// sumbit 后返回result
Executors.callable（Runnable task，Object result）
```
任务的执行：  


# Executor框架的使用

示意图

![executor2](https://raw.githubusercontent.com/FameLsy/Images/master/thread/executor2.png)

执行流程
1. 主线程创建实现Runnable或者Callable接口的任务对象
2. Runnable对象直接交给ExecutorService执行;或者把Runnable对象或Callable对象提交给ExecutorService执行

```java
//直接执行
ExecutorService.execute（Runnablecommand）;
//提交,会返回实现Future接口的对象（一般是FutureTask对象）
//FutureTask实现了Runnable，程序员也可以创建FutureTask，然后直接交给ExecutorService执行。
Executor-Service.submit（Runnable task）
ExecutorService.submit（Callable<T>task）
```

3. 主线程可以执行FutureTask.get()方法来等待任务执行完成。主也可以执行FutureTask.cancel（boolean mayInterruptIfRunning）来取消此任务的执行。

# 
