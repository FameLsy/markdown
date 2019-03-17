---
title: 队列（Queue）
tags: 
- 数据结构
categories: 
- 数据结构
---


# 队列（Queue）

队列（Queue）
- 具有一定操作约束的线性表，只能在一端插入，在另一端删除。
- 数据插入：入队列（addQ）
- 数据删除：出队列（deleteQ）
- 先进先出：FIFO

操作：
```
Queue createQueue(): 生成空队列
boolean isFullQ(): 队列是否已满
void addQ(E element): 插入元素到队列
boolean isEmptyQ(): 是否为空
E deleteQ(): 将队头数据从队列中删除并返回
```

## 队列的顺序存储实现

结构：由一个一维数组和一个记录队列头元素位置的变量front和一个记录队尾元素位置的变量rear组成

初始化
```java
public class QueueImpl<E> implements Queue<E> {
    private Object[] data;
    private int front = -1;//头元素位置，初始-1，删除时+1
    private int rear = -1;//队尾元素位置，初始-1，添加时+1
}
```

addQ操作
```java
@Override
@SuppressWarnings("unchecked")
public void addQ(Object element) {
    int remainder = (rear + 1) % data.length;//+1求余，可以进行循环操作数组
    if( remainder == front) return; //当+1后恰好等于front,则表示队列满
    rear = remainder;//改变rear的值（其实已经执行了+1操作）
    data[rear] = (E)element;
}
```

deleteQ操作
```java
@Override
@SuppressWarnings("unchecked")
public E deleteQ() {
    if(front == rear) return null; //相等时队列为空
    else {
        front = (front+1) % data.length;
        return (E)data[front];
    }
}
```

## 队列的链式存储实现

front和rear只能指向链表头（链表为单向，单项的链表用链表尾时不可能做删除操作的【取不到前一个节点】）

初始化：
- 两个node结构
- Node形成链
- QNode形成队列的节点结构，指向队尾和队头两个节点。

```java
public class LinkedQueue<E> implements Queue {
    private QNode qNode;

    static class Node<E>{
        E element;
        Node nextNode;//指向节点
    }

    static class QNode{
        Node rear;//指向队尾节点
        Node front;//指向队头节点
    }
}
```

deleteQ操作
```java
@Override
@SuppressWarnings("unchecked")
public Object deleteQ() {
    Node front = qNode.front;//队列头指向的节点
    Node rear = qNode.rear;//队列尾指向的节点
    E element = null;//节点元素
    if (front == null) return null; //如果队列头节点是空的，那整个队列肯定都是空的
    if (front == rear) front = rear = null;//头尾相等，表示只有一个节点，删除后为null了
    else {
        element = (E)front.element;//把节点的值赋值给element
        front = front.nextNode;// 头节点指向下一个节点
    }
    return element;
}
}
```