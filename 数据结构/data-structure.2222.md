---
title: 堆栈(Stack)
tags: 
- 数据结构
categories: 
- 数据结构
---


# 堆栈(Stack)：

堆栈(Stack)
- 具有一定操作约束的线性表，只在一端（栈顶，top）做插入、删除
- 插入数据：入栈（push）
- 删除数据：出栈（pop）
- 后入先出：last in first out (LIFO)

操作：
```
Stack createStack(int maxSize): 生成空堆栈，其最大长度为maxSize
int isFull(): 判断堆栈S是否已满
void push(E element): 将元素压入堆栈
int isEmpty: 判断堆栈是否为空
E pop(): 删除并返回栈顶元素
```

## 栈的顺序存储实现

栈的顺序存储结构
- 一个一维数组
- 一个记录元素位置的变量组成

初始化：
```java

public class StackImpl<E> implements Stack {
    private Object[] data = {};// 数据存储
    int top = -1;//栈顶元素位置,-1表示空
}
```

push操作
```java
/**
* push操作
* @param element 需要push的值
*/
@Override
public void push(Object element) {
    if (top == data.length-1) return; //此时栈满，无法push
    else {
        data[++top] = element;//没满就push
    }
}
```

pop操作

```java
/**
* pop 操作
* @return 值
*/
@SuppressWarnings("unchecked")// 忽略强制类型转换成(E)的警告
@Override
public E pop() {
    if (top == -1) return null;//堆栈空
    else return (E)data[top--];// 这个top--用的可以，先return top位置的data后，栈顶位置-1
}
```

补充：一个数组实现两个堆栈

原理：将数组一份为二，分成左右两个栈，左边从左往右存数据，右边从右往左存数据，当top1 == top2时，表示栈满

```java

public class TwoStack<E> implements Stack{
private Object[] data = {};
private int top1 = -1; // 栈1栈顶
private int top2 = data.length-1;//栈2栈顶

/**
* push操作
* @param element 要插入的数
* @param tag 要放入哪个栈
*/
public void push(E element, int tag){
    if(top1 == top2) return; //相等时。栈满
    if (tag == 1) data[++top1] = element;//tag == 1，放入栈1
    else data[--top2] = element;// tag 不等于1，放入栈2
}

/**
* pop操作
* @param element 要插入的数
* @param tag 要放入哪个栈
* @return
*/
@SuppressWarnings("unchecked")
public E pop(E element, int tag){
    if (tag == 1){
        if (top1 == -1) return null;//等于-1，空栈，无法pop
        else return (E)data[top1--];//返回值，并top -1
    }
    else{
        if (top2 == data.length-1) return null; //空栈，无法pop
        else return (E)data[top2++];//返回值，并top + 1
    }

}
```

## 堆栈的链式存储实现

栈的链式存储结构实际上是单链表，叫做链栈。插入和删除操作只能在链栈的栈顶进行。

栈顶Top指向链表的指向第一个节点（为什么不能指向最后一个节点，因为在pop操作时，你无法获取到前一个节点，自然无法删除当前节点）

初始化操作：topNode仅仅是栈头节点，只用来指向第一个节点（即用来指向栈顶）


```java
public class LinkedStack<E> implements Stack{
    private Node<E> topNode;//栈头节点，初始值只用来指向下一个节点

    public LinkedStack() {
        this.topNode = new Node<>();
    }

    static class Node<E>{
        E element;
        Node<E> nextNode;
        Node(){};//默认构造函数
        Node(E element, Node<E> nextNode){
            this.element = element;
            this.nextNode = nextNode;
        }
    }
}
```

判空操作
```java
@Override
public boolean isEmpty() {
    //栈头节点没有指向节点，说明是空栈
    return topNode.nextNode == null;
}
```

push操作
```java
/**
* push操作
* @param element
*/
@Override
@SuppressWarnings("unchecked")
public void push(Object element) {
    topNode.nextNode = new Node<>((E) element, topNode.nextNode);//将栈头指向的node 赋值给新node，栈顶指向新node

}
```

pop操作
```java
/**
* pop 操作
* @return 返回元素
*/
@Override
public E pop() {
    if(this.isEmpty()) return null;// 栈空
    else {
        Node<E> nextNode = topNode.nextNode;//获取第一个节点
        topNode.nextNode = nextNode.nextNode;//栈顶节点指向第二个节点
        return (E)nextNode.element;//返回第一个节点的值
    }
}
```

