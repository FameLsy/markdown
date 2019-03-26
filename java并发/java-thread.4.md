# 线程其他方法

使线程休眠
```java
//单位毫秒。抛出InterruptedException异常
 public static native void sleep(long millis) throws InterruptedException;
 ```

返回代表当前执行线程的对象
```java
//静态方法
Thread.currentThread();
```

过期方法,原因是无法保证能释放占有的资源，容易引发死锁
```java
suspend()：暂停  
resume()：恢复  
stop()：停止  
```