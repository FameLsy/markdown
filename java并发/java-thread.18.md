<!-- Fork/Join框架 -->

Fork/Join框架是Java 7提供的一个用于并行执行任务的框架，是一个把大任务分割成若干个小任务，最终汇总每个小任务结果后得到大任务结果的框架。

# 工作窃取算法

工作窃取（work-stealing）算法
- 指某个线程从其他队列里窃取任务来执行.
- 通常会使用双端队列，被窃取任务线程永远从双端队列的头部拿任务执行
- 窃取任务的线程永远从双端队列的尾部拿任务执行
- 优点:充分利用线程进行并行计算，减少了线程间的竞争
- 缺点:在某些情况下还是存在竞争，比如双端队列里只有一个任务时。并且该算法会消耗了更多的系统资源，比如创建多个线程和多个双端队列

# Fork/Join框架的设计

步骤
1. 分割任务，通过fork类将任务切割成足够小的子任务
2. 执行任务并合并结果，子任务放入双端队列中，多个线程从队列中获取；子任务执行结果放在同一个队列里，启动一个线程合并结果

Fork/Join使用两个类完成任务
1. ForkJoinTask,创建ForkJoin任务，提供fork()和join()方法,一般继承它的子类
    RecursiveAction：用于没有返回结果的任务。
    RecursiveTask：用于有返回结果的任
2. ForkJoinPool,ForkJoinTask需要通过ForkJoinPool来执行

任务分割出的子任务会添加到当前工作线程所维护的双端队列中，进入队列的头部。当一个工作线程的队列里暂时没有任务时，它会随机从其他工作线程的队列的尾部获取一个任务。

# 使用Fork/Join框架

以计算1+2+3+4的结果为例
1. 假设每个子任务最多执行两个数的相加，则设置分割的阈值是2
2. 因为是有结果的任务，所以必须继承RecursiveTask

```java

public class CountTask extends RecursiveTask<Integer> {
    private static final int THRESHOLD = 2;　　// 阈值
    private int start;
    private int end;
    public CountTask(int start, int end) {
        this.start = start;
        this.end = end;
    }
    @Override
    protected Integer compute() {
        int sum = 0;
// 如果任务足够小就计算任务
        boolean canCompute = (end - start) <= THRESHOLD;
        if (canCompute) {
            for (int i = start; i <= end; i++) {
                sum += i;
            }
        } else {
// 如果任务大于阈值，就分裂成两个子任务计算
            int middle = (start + end) / 2;
            CountTask leftTask = new CountTask(start, middle);
            CountTask rightTask = new CountTask(middle + 1, end);
// 执行子任务
            leftTask.fork();
            rightTask.fork();
// 等待子任务执行完，并得到其结果
            int leftResult=leftTask.join();
            int rightResult=rightTask.join();
// 合并子任务
            sum = leftResult + rightResult;
        }
        return sum;
    }
    public static void main(String[] args) {
        ForkJoinPool forkJoinPool = new ForkJoinPool();
// 生成一个计算任务，负责计算1+2+3+4
        CountTask task = new CountTask(1, 4);
// 执行一个任务
        Future<Integer> result = forkJoinPool.submit(task);
        try {
            System.out.println(result.get());
        } catch (InterruptedException e) {
        } catch (ExecutionException e) {
        }
    }
}
```

# Fork/Join框架的异常处理

检查任务是否已经抛出异常或已经被取消

```java
aForkJoinTask.isCompletedAbnormally()
```

获取异常
```java
//返回Throwable对象，如果任务被取消了则返回CancellationException;任务没有完成或者没有抛出异常则返回null
aForkJoinTask.getException()
```

# Fork/Join框架的实现原理