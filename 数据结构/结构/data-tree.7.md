<!-- ---
title: 二叉树
tags: 
- 二叉树
categories: 
- 数据结构
--- -->

# 树

关于“树”，还有三个比较相似的概念：高度（Height）、深度（Depth）、层（Level）

![树](https://raw.githubusercontent.com/FameLsy/Images/master/data/树.png)

![树2](https://raw.githubusercontent.com/FameLsy/Images/master/data/树2.png)

## 二叉树

二叉树
- 每个节点最多有两个“叉”，也就是两个子节点，分别是左子节点和右子节点。
- 二叉树并不要求每个节点都有两个子节点，有的节点只有左子节点，有的节点只有右子节点。

满二叉树
- 除了叶子节点之外，每个节点都有左右两个子节点

![满二叉树](https://raw.githubusercontent.com/FameLsy/Images/master/data/满二叉树.png)

完全二叉树
- 叶子节点都在最底下两层
- 最后一层的叶子节点都靠左排列
- 并且除了最后一层，其他层的节点个数都要达到最大

![完全二叉树](https://raw.githubusercontent.com/FameLsy/Images/master/data/完全二叉树.png)

## 二叉树的存储

有两种方法
1. 基于指针或者引用的二叉链式存储法
2. 基于数组的顺序存储法。

### 链式存储法

核心思想：
1. 每个节点有三个字段，其中一个存储数据，另外两个是指向左右子节点的指针

![链式存储法](https://raw.githubusercontent.com/FameLsy/Images/master/data/链式存储法.png)

### 顺序存储法

核心思想
1. 把根节点存储在下标1的位置(设为1为根节点，方便计算)
2. 如果节点为i,其左子节点存储在下标2 * i的位置
3. 如果节点为i,其右子节点存储在2 * i 的位置

![顺序存储法](https://raw.githubusercontent.com/FameLsy/Images/master/data/顺序存储法.png)


如果是完全二叉树，那么顺序存储法不会浪费空间，(不需要像链式存储法那样，要存储额外的左右子节点的指针)。但如果是非完全二叉树，会浪费比较多的数组存储空间

