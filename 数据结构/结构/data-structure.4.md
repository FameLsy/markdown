---
title: 队列
tags: 
- 队列
categories: 
- 数据结构
---

# 队列

什么是队列？
- 类似于排队买午饭，先进先出
- 最基本操作为入队  
    enqueue(): 放一个数据到尾部  
    dequeue(): 从队列头部取一个数据  

高性能队列Disruptor、Linux环形缓存，都用到了循环并发队列；java.concurrent并发包利用ArrayBlockingQueue实现公平锁

![queue](https://raw.githubusercontent.com/FameLsy/Images/master/data/queue.png)

## 常见队列

### 顺序队列代码

特点：
- 队列需要两个指针：头指针head，指向队头；尾tail，指向队尾
- 当进行入队操作后，tail指针右移
- 当进行出队操作后，head指针右移

```java
// 用数组实现的队列
public class ArrayQueue {
    // 数组：items，数组大小：n
    private String[] items;
    private int n = 0;
    // head表示队头下标，tail表示队尾下标
    private int head = 0;
    private int tail = 0;

    // 申请一个大小为capacity的数组
    public ArrayQueue(int capacity) {
        items = new String[capacity];
        n = capacity;
    }
// 入队
    public boolean enqueue(String item) {
        // 如果tail == n 表示队列已经满了
        if (tail == n) return false;
        items[tail] = item;
        ++tail;
        return true;
    }
    // 出队
    public String dequeue() {
        // 如果head == tail 表示队列为空
        if (head == tail) return null;
        String ret = items[head];
        ++head;s
        return ret;
    }
}
```

如果tail==n时，则无法继续往队列中加数据，但很有可能队列前面还有空间，所以，需要触发一次搬移操作，此时修改enqueue()操作

```java
public boolean enqueue(String item) {
    // tail == n表示队列末尾没有空间了
    if (tail == n) {
    // tail ==n && head==0，表示整个队列都占满了
    if (head == 0) return false;
    // 数据搬移
    for (int i = head; i < tail; ++i) {
    items[i-head] = items[i];
    }
    // 搬移完之后重新更新head和tail
    tail -= head;
    head = 0;
    }
    items[tail] = item;
    ++tail;
    return true;
}
```


![queue2](https://raw.githubusercontent.com/FameLsy/Images/master/data/queue2.png)

### 基础链表的队列实现

要想实现基于链表的队列
- 需要两个指针，head指针和tail指针
- 入队：tail->next = new_node,tail = tail->next
- 出队：head = head->next
如图

![queue3](https://raw.githubusercontent.com/FameLsy/Images/master/data/queue3.png)

### 循环队列

当tail==n时，前面做法时通过数据搬移来解决，为了避免使用数据搬移，可以使用循环队列，即tail==n时，让它变为tail=0,而不是+1

![queue4](https://raw.githubusercontent.com/FameLsy/Images/master/data/queue3.png)

若要实现循环队列，必须考虑两种情况
- 队列空：一般判断条件是tail==head
- 队列满：一般判断条件是tail==n

对于队列空，依旧可以使用tail==head来判断，但对于队列满，tail==n已经不适用了，而是有这样一条规律：
```
(tail+1)%n=head
```

实现代码为
```java
// 入队
public boolean enqueue(String item) {
    // 队列满了
    if ((tail + 1) % n == head) return false;
    items[tail] = item;
    tail = (tail + 1) % n;
    return true;
}
// 出队
public String dequeue() {
    // 如果head == tail 表示队列为空
    if (head == tail) return null;
    String ret = items[head];
    head = (head + 1) % n;
    return ret;
    }
}
```

### 阻塞队列

阻塞队列其实就是在队列基础上加了阻塞操作
- 队列空时，从对头取数据会被阻塞，知道队列中有了数据才能返回
- 队列满，插入如数据操作会被阻塞，知道队列有空闲位置再插入数据，然后返回
- (典型的生产者-消费者模型)

### 并发队列

并发队列即线程安全的队列
- 最简单实现方式是直接再enqueue()、dequeue()上枷锁，锁力度大，并发度低

# 队列相关应用

## 线程池没有空闲线程时，如何处理新的请求线程资源

线程池底部数据结构，就是使用了队列

一般解决使用两种策略
- 非阻塞式：直接拒绝任务请求
- 阻塞式：将请求排队，等到有空闲线程时，取出排队的请求继续处理

如何来设计存储请求排队？
- 第一种：基于链表实现队列，可无限制排队，对响应时间敏感的系统不合适
- 第二种：基于数组实现队列，比较适合对响应时间敏感的系统，但对队列的大小设计有一定要求
