---
title: 链表(LinkedList)
tags: 
- 链表
categories: 
- 数据结构
---



# 链表

什么是链表？
1. 链表是通过将零散的内存块串联起来，该内存块被称为"结点"；
2. 结点除了存储数据之外，还需要记录链上的下一个结点的地址，该地址被称为"后继指针next"

## 链表与数组的区别

区别如下：
1. 数组需要连续的空间，如1000MB的数组，必须要有1000MB的连续空间，否则就算内存有充足的空间但不连续，数组就申请失败;而链表无需连续的内存空间，通过“指针”将零散的内存快串联起来
2. 数组大小固定，而链表没有大小限制
3. 数组使用连续内存空间，可借助CPU缓存机制，预读数组中的数据，访问效率更高；链表没有办法有效预读

![linkedlist](https://raw.githubusercontent.com/FameLsy/Images/master/data/linkedlist.png)

||||
|--|--|--|
|时间复杂度|数组|链表|
|插入/删除|O(n)|O(1)|
|随机访问|0(1)|O(n)|


## 常见链表

### 单链表

单链表存在两个特殊的结点
- 第一个结点：称为头结点，用来记录链表的基地址
- 最后一个结点：尾结点，指针指向NULL

![linkedlist2](https://raw.githubusercontent.com/FameLsy/Images/master/data/linkedlist2.png)



删除和插入操作
- 只需要改变插入位置相邻结点的指向极客
- 时间复杂度为O(1)

![linkedlist3](https://raw.githubusercontent.com/FameLsy/Images/master/data/linkedlist3.png)

查询操作
- 因为链表数据并非连续存储，所以无法想数组一样靠首地址和下标，只能通过遍历结点获取
- 时间复杂度：O(n)

### 循环链表

属于一种特殊的单链表
- 循环链表的尾结点指向链表的头结点
- 优点在于处理数据具有环形结构特点比较方便，如约瑟夫问题

![linkedlist4](https://raw.githubusercontent.com/FameLsy/Images/master/data/linkedlist4.png)

### 双向链表

特点：
- 每个结点不知有一个后继指针next,还有一个前驱指针prev
- 因为存储后继结点和前驱结点，需要额外的空间
- 双向链表查找前驱结点的时间复杂度是O(1)
- 双向链表再某些情况下插入删除操作比单链表更简单高效

在java中，LinkedHashMap使用了双项链表结构

![linkedlist5](https://raw.githubusercontent.com/FameLsy/Images/master/data/linkedlist5.png)

---
单链表的插入删除操作已经是O(1),双向链表如何更高效？

通常开发中的删除数据情况
- 情况1：给定某个值，删除对应结点
- 情况2：给定某个指针，删除给定指针指向的结点

对于情况1，具体流程为：
- 查找需要的结点（需遍历结点，复杂度O(n)）
- 改变相邻结点指针指向（复杂度O(1))
- 总的时间复杂度为O(n)

对于情况2，直接获取到需要删除的结点q，但需要知道该结点的前驱结点
- 对于单链表，为了找前驱结点p，还是需要遍历链表，直到p.next=q,复杂度为0(n)
- 对于双向链表，复杂度就是O(1)了

链表的插入同理

---

## 双向循环链表

![linkedlist6](https://raw.githubusercontent.com/FameLsy/Images/master/data/linkedlist6.png)




# 如何写好链表

## 理解指针

所谓指针：
- 将某个标量赋值给指针，就是将变量的地址复制给指针。通过指针保存的内存地址，找到这个变量
- 对于java，就是引用

常用的代码
```java
//p结点中的next针织存储了q结点的地址
p->next = q
//p结点next指针存储p结点的下下个结点内存地址（删除了下一个结点）
p->next = p->next->next
```

## 警惕指针丢失和内存泄露

假设想将x插入到p和b之间

如下代码，会导致指针丢失和内存泄漏
```java
//p指向了自己
p->next = x;
x->next = p->next;
```
正确的插入x的做法应该是
```java
//先将x指向b
x->next = p->next;
//再将p指向x
p->next = x;
```

## 利用哨兵简化实现难度

假设在头结点p后插入新结点x

```java
x->next = p->next;
p->next = x;
```

看似没有问题，但如果该链表其实是空表呢?(需要插入到表头)

对于插入到头结点head，与其他插入逻辑不同

```java
if(head == null){
    head = x;
}
```

再来看删除操作
```java
p->next = p->next->next
```
正常来讲没有问题，但如果删除的是链表的最后一个结点，那就出错了,需要进行特殊处理
```java
if(head->next == null){
    head = null
}
```

可以看出，对于头尾结点，需要特殊处理。哨兵的作用，就是解决这样的“边界问题”
- 拥有哨兵的结点，被称为带头链表
- 不管链表是否为空，head结点永远指向哨兵结点
- 哨兵结点不存储数据
- 好处就是对于插入第一个结点和其他结点，或者删除最后一个结点和删除其他结点，可以统一为相同的逻辑代码了

![linkedlist7](https://raw.githubusercontent.com/FameLsy/Images/master/data/linkedlist7.png)

## 留意边界条件处理

写完链表后，检查边界条件下是否可以正常允许，通常考虑
- 链表为空的情况
- 链表只包含一个结点
- 链表只包含两个结点
- 代码逻辑处理头结点和尾结点

## 画图

利用画图，更加清醒插入、删除等操作

比如：
![linkedlist8](https://raw.githubusercontent.com/FameLsy/Images/master/data/linkedlist8.png)

# 链表应用场景

链表经典的应用场景，就是LRU缓存淘汰算法

缓存
- 一种提高数据读取性能的技术
- 大小有限，缓存被用满时，需要决定删除那些数据，此时就需要缓存淘汰策略

缓存淘汰策略，常见有三种
- 先进先出FIFO（First In, First Out）
- 最少使用策略LFU(Least Frequently Used)
- 最近最少使用策略LRU(Least Recently Used)

## LRU缓存淘汰算法

思路：
- 维护一个有序单链表
- 越靠近链表尾结点，说明是越早之前访问的
- 一个新的数据被访问，从链表头开始顺序遍历链表
- 如果此数据已经缓存在链表内，将这个数据对应的结点从原来的位置删除，插入到链表表头
- 否则，有可以分为两种情况
- 如果此时缓存未满，则直接将该数据对应的结点插入表头
- 否则，则删除链表尾结点，数据对应的结点插入表头