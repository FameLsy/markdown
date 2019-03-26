
# 线程的属性

## 线程优先级

Thred类定义了三个优先级常量和一个成员变量 *priority* ，利用该成员变量来控制优先级;优先级的范围: 1~10,默认5

```java
    private int priority;
  /**
     * The minimum priority that a thread can have.
     */
    public final static int MIN_PRIORITY = 1;

   /**
     * The default priority that is assigned to a thread.
     */
    public final static int NORM_PRIORITY = 5;

    /**
     * The maximum priority that a thread can have.
     */
    public final static int MAX_PRIORITY = 10;
```





设置优先级  
```java
aThread.setPriority(int level)
```

需要注意的是，优先级收操作系统的影响，如在ubuntu下会忽略线程优先级



## 守护线程

Thread类定义了一个成员变量 *daemon* 来标识该线程是否为守护线程,a首位的守护线程是一种支持型线程,它主要被用作程序中后台调度以及支持性工作，当只有守护线程时，Java虚拟机会退出 
```java
    /* Whether or not the thread is a daemon thread. */
    private boolean     daemon = false;
```

将线程设置成守护线程
```java
aThread.setDaemon(true);
```

需要注意的是
1. Daemon属性需要在启动线程之前设置，不能在启动线程之后设置
2. Daemon中的finally块并不一定执行(JVM提前退出)

## 未捕获异常处理器

Thread中定义了两个处理器，用来处理run()方法可能出现的异常。run()方法不能抛出受查异常，而非受查异常会导致线程终止，但在线程终止之前，异常会被传到处理器中。

```java
  //如果不设置，则未捕获异常处理器为空
   // null unless explicitly set
    private volatile UncaughtExceptionHandler uncaughtExceptionHandler;

    // null unless explicitly set
    //静态的默认处理
    private static volatile UncaughtExceptionHandler defaultUncaughtExceptionHandler;
```

设置未捕获异常处理器

```java
public void setUncaughtExceptionHandler(UncaughtExceptionHandler eh)

// 静态方法为每一个线程设置一个默认的处理器
public static void setDefaultUncaughtExceptionHandler(UncaughtExceptionHandler eh)

```

如果没有设置未捕获异常处理器的话，那么默认的异常处理器为java.lang.ThreadGroup类,该类是UncaughtExceptionHandler的实现类，其处理异常方式如下
1. 该线程有父线程，则执行父线程的uncaughtException()方法;没有，则进入2
2. 调用Thread.getDefaultUncaughtExceptionHandler(),调用静态的默认处理器；如果返回null,则进入3
3. 如果异常(Throwable)是TreadDeath的一个实例，则什么都不做；否则，进入4
4. 线程名字一集Throwable的栈轨迹输出到System.err上

```java
    public void uncaughtException(Thread t, Throwable e) {
        if (parent != null) {
            parent.uncaughtException(t, e);
        } else {
            Thread.UncaughtExceptionHandler ueh =
                Thread.getDefaultUncaughtExceptionHandler();
            if (ueh != null) {
                ueh.uncaughtException(t, e);
            } else if (!(e instanceof ThreadDeath)) {
                System.err.print("Exception in thread \""
                                 + t.getName() + "\" ");
                e.printStackTrace(System.err);
            }
        }
    }
```


