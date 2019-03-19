<!-- ---
title: 二叉查找树
tags: 
- 二叉查找树
categories: 
- 数据结构
--- -->

# 二叉查找树

特点
- 支持动态数据集合的快速插入、删除、查找操作
- 在树中的任意一个节点，其左子树中的每个节点的值，都要小于这个节点的值，而右子树节点的值都大于这个节点的值

![二叉查找树](https://raw.githubusercontent.com/FameLsy/Images/master/data/二叉查找树.png)

## 二叉查找树的查找操作

核心思想
- 先取根节点，如果它等于我们要查找的数据，那就返回
- 如果要查找的数据比根节点的值小，那就在左子树中递归查找
- 如果要查找的数据比根节点的值大，那就在右子树中递归查找

```java
public class BinarySearchTree {
    private Node tree;
    public Node find(int data) {
        Node p = tree;
        while (p != null) {
            if (data < p.data) p = p.left;
            else if (data > p.data) p = p.right;
            else return p;
        }
        return null;
    }
    public static class Node {
        private int data;
        private Node left;
        private Node right;
        public Node(int data) {
            this.data = data;
        }
    }
}
```

## 二叉查找树的插入操作

核心思想
- 类似查找操作
- 从根节点开始
- 如果要插入的数据比节点的数据大，并且节点的右子树为空，就将新数据直接插到右子节点的位置；如果不为空，就再递归遍历右子树，查找插入位置
- 如果要插入的数据比节点数值小，并且节点的左子树为空，就将新数据插入到左子节点的位置；如果不为空，就再递归遍历左子树，查找插入位置

```java
public class demo {
    public void insert(int data) {
        if (tree == null) {
            tree = new Node(data);
            return;
        }
        Node p = tree;
        while (p != null) {
            if (data > p.data) {
                if (p.right == null) {
                    p.right = new Node(data);
                    return;
                }
                p = p.right;
            } else { // data < p.data
                if (p.left == null) {
                    p.left = new Node(data);
                    return;
                }
                p = p.left;
            }
        }
    }
}
```

## 二叉查找树的删除操作

较为复杂，需要分三种情况处理
1. 如果要删除的节点没有子节点，我们只需要直接将父节点中，指向要删除节点的指针置为null(如图删除节点55)
2. 如果要删除的节点只有一个子节点（只有左子节点或者右子节点），我们只需要更新父节点中，指向要删除节点的指针，让它指向要删除节点的子节点就可以了。（如图删除节点13）
3. 如果要删除的节点有两个子节点,需要找到这个节点的右子树中的最小节点，把它替换到要删除的节点上。然后再删除掉这个最小节点，因为最小节点肯定没有左子节点（如果有左子结点，那就不是最小节点了）（如图删除节点18）

![二叉树删除](https://raw.githubusercontent.com/FameLsy/Images/master/data/二叉树删除.png)

```java
public void delete(int data) {
        Node p = tree; // p指向要删除的节点，初始化指向根节点
        Node pp = null; // pp记录的是p的父节点
        while (p != null && p.data != data) {
            pp = p;
            if (data > p.data) p = p.right;
            else p = p.left;
        }
        if (p == null) return; // 没有找到
        // 要删除的节点有两个子节点
        if (p.left != null && p.right != null) { // 查找右子树中最小节点
            Node minP = p.right;
            Node minPP = p; // minPP表示minP的父节点
            while (minP.left != null) {
                minPP = minP;
                minP = minP.left;
            }
            p.data = minP.data; // 将minP的数据替换到p中
            p = minP; // 下面就变成了删除minP了
            pp = minPP;
        }
    // 删除节点是叶子节点或者仅有一个子节点
        Node child; // p的子节点
        if (p.left != null) child = p.left;
        else if (p.right != null) child = p.right;
        else child = null;
        if (pp == null) tree = child; // 删除的是根节点
        else if (pp.left == p) pp.left = child;
        else pp.right = child;
}
```

## 支持重复数据的二叉查找树

方法一：二叉查找树中每一个节点不仅会存储一个数据，通过链表和支持动态扩容的数组等数据结构，把值相同的数据都存储在同一个节点上。

方法二：
- 每个节点仍然只存储一个数据。在查找插入位置的过程中，如果碰到一个节点的值，与要插入数据的值相同，就将这个要插入的数据放到这个节点的右子树，也就是说，把这个新插入的数据当作大于这个节点的值来处理

![重复插入](https://raw.githubusercontent.com/FameLsy/Images/master/data/重复插入.png)

- 对于查找操作，，遇到值相同的节点，我们并不停止查找操作，而是继续在右子树中查找，直到遇到叶子节点，才停止。这样就可以把键值等于要查找值的所有节点都找出来。

![重复查找](https://raw.githubusercontent.com/FameLsy/Images/master/data/重复查找.png)

- 对于删除操作，需要先查找到每个要删除的节点，然后再按前面讲的删除操作的方法，依次删除

![重复删除](https://raw.githubusercontent.com/FameLsy/Images/master/data/重复删除.png)


