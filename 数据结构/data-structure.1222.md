---
title: 数据结构
tags: 
- 数据结构
categories: 
- 数据结构
---


# 线性表（List）

线性表：
- 由同类型数据元素构成的有序序列的线性结构
- 表中元素的个数称为线性表的长度
- 线性表没有元素时，称为空表
- 表起始位置称 表头 ，表结束位置称 表尾 

线性表的主要操作：
```
List(): 初始化List
Object findKth(int k): 根据K来查找相应位置的元素
int find(Object x): 在List中查找X的第一次出现的位置
void insert(Object x, int i): 在i位置前插入一个x
void delete(i): 删除指定位置i的元素
int length(): 返回List的长度
```

## 线性表的顺序存储实现(ArrayList)

利用数组的连续存储空间顺序存放线性表的各元素

基本构成：
- 用于存放数据的数组,数组的大小即长度
- 最后一个数组元素的索引

```java
public class ArrayList {
        private Object[] elementData;//用于存放数据的数组
        private int last;//最后一个数组元素的索引
}
```



初始化操作： 
```java
public List() {
    //空数组
    this.elementData =new Object[0];
    //last设为-1
    this.last = -1;
}
```

查询元素所在位置：
```java
public int find(Object x){
    int i = 0;
    //从0开始找直到最后或者找到x，停止循环
    while (i <= last && elementData[i] != x) i++;
    //i 大于last时，表示找到最后一个没找到，返回-1
    if (i > last) return -1;
    //找到的话直接返回i就是x所在的位置
    else return i;
}
```

插入操作：
```java
 /**
* 
* @param x 要插入的元素
* @param i 要插入的位置，i >= 1,因为0前面是没有索引的
*/
public void insert(Object x, int i){
    // 如果last指向最后一个位置，表示空间已满，不能插入
    if (last == elementData.length-1) return;
    //检查插入位置的合法性
    if (i < 1 || i > last + 2) return;

    for (int j = last; j >= i-1; j--){
        elementData[j + 1] = elementData[j];//从最后一个数开始往后存数
        elementData[i-1] = x; //将x插入
    }
    last++; //last仍指向最后的元素
    return;
}
```

删除操作
```java
    public void delete(int i){
        //检查空表以及删除位置的合法性
        if (i <0 || i > last+1) return;

        for(int j = i; j <= last; j++){
            if (j == last) elementData[last] = null;
            else elementData[j] = elementData[j + 1]; // 从i位置一个一个往前挪
        }
        last--;//last仍指向最后的元素
        return;
    }

```


## 线性表的链式存储实现(LinkedList)

链式存储:
- 通过"链"建立数据元素之间的逻辑关系
- 插入、删除不需要移动数据元素，只需要修改“链”
- 主要是通过节点，来存储元素，以及指向下一个节点

初始化操作：
```java
public class LinkedList<E> {

Node<E> node;// 链表形式的List含有Node（节点）

/**
* 节点拥有两个属性
* element : 表示该节点的元素
* nextNode : 指向下一个节点
* @param <E>
*/
static class Node<E>{
    E element;//节点的元素
    Node<E> nextNode;//指向下一个节点
    Node(E element, Node<E> nextNode){
        this.element = element;
        this.nextNode = nextNode;
    }
}
```

按位置查找值：通过遍历Node节点，来进行查找
```java
/**
* 按位置查找
* @param k node的位置
* @return
*/
Node<E> findKth(int k){
    Node<E> newNode = node;
    int i = 0;
    //不断循环遍历，直到node为0或者i == k
    while (newNode != null && i < k){
        newNode = newNode.nextNode;
        i++;
    }
    return newNode;
}
```

按值查找位置
```java
/**
* 按值查找节点
* @param element 元素值
* @return
*/
Node<E> find(E element){
    //循环遍历直到找到相同的element
    while (node != null && element != node.element){
        node = node.nextNode;
    }
    return node; //当循环内执行完毕后，如果没找到，此时node就是null了
}
```

插入：
```java
/**
* 插入元素
* @param element 要插入的元素
* @param i 要插入的位置
* @return 1插入成功，-1插入失败
*/
int insert(E element, int i){
    //在表头插入
    if(i == 0){
        node = new Node<E>(element, node);
        return 1;
    }
    Node<E> eNode = findKth(i-1);//查找i-1位置的节点
    if (eNode == null) return -1; //如果该位置的并不存在节点，则不能插入
    else {
        Node<E> newNode = new Node<E>(element, eNode.nextNode);//新的节点指向前一个节点的下一个Node
        eNode.nextNode = newNode; //前一个节点指向新的节点
        return 1;
    }
}
```

删除操作
```java
/**
* 删除操作
* @param i 需要删除的位置
* @return 1表示删除成功，-1表示删除失败
*/
int delete(int i){
    //插入头节点
    if (i == 0) {
        if(node == null) return -1; //node为null，则本身就是个空表，无法删除
        else node = node.nextNode;//将头节点指向下一个节点
        return 1;
    }
    //不是头节点
    Node<E> eNode = findKth(i-1);//获取i-1位置的节点
    if (eNode == null) return -1;//没有该节点，则不能删除
    else if(eNode.nextNode == null) return -1;//即i位置的没有节点，也不能删除
    else {
        eNode.nextNode = eNode.nextNode.nextNode;
        return 1;
    }
}
```

从表头添加元素操作（自写，为了方便测试）
```java
    /**
     * 添加方法
     * @param element 需要添加的元素
     * @return
     */
    int add(E element){
        node = new Node<E>(element, node);
        return 1;
    }

```


测试类
```java

public class LinkedListTest {
    @Test
    public void linkedListTest(){
        LinkedList<String> stringLinkedList = new LinkedList<>();
        stringLinkedList.add("1");
        stringLinkedList.add("2");
        stringLinkedList.add("3");
        System.out.println(stringLinkedList.toString());

        stringLinkedList.insert("x", 0);
        System.out.println("插入：" + stringLinkedList.toString());

        stringLinkedList.delete(0);
        System.out.println("删除：" + stringLinkedList.toString());

        LinkedList.Node<String> kth = stringLinkedList.findKth(2);
        System.out.println("获取:" +  kth.toString());

        kth = stringLinkedList.find("2");
        System.out.println("获取:" + kth.toString());
    }

}
```

测试结果
```
LinkedList{node=Node{element=3, nextNode=Node{element=2, nextNode=Node{element=1, nextNode=null}}}}
插入：LinkedList{node=Node{element=x, nextNode=Node{element=3, nextNode=Node{element=2, nextNode=Node{element=1, nextNode=null}}}}}
删除：LinkedList{node=Node{element=3, nextNode=Node{element=2, nextNode=Node{element=1, nextNode=null}}}}
获取:Node{element=1, nextNode=null}
获取:Node{element=2, nextNode=Node{element=1, nextNode=null}}
其中，stringLinkedList的详细参数确实形成了链表结构
```