---
title: 二叉树的遍历
tags: 
- 二叉树的遍历
categories: 
- 数据结构
---

# 二叉树的遍历

二叉树遍历有三种经典的方法
1. 前序遍历（对于树中的任意节点来说，先打印这个节点，然后再打印它的左子树，最后打印它的右子树。）
2. 中序遍历（对于树中的任意节点来说，先打印它的左子树，然后再打印它本身，最后打印它的右子树。）
3. 后序遍历（对于树中的任意节点来说，先打印它的左子树，然后再打印它的右子树，最后打印这个节点本身。）

![二叉树遍历](https://raw.githubusercontent.com/FameLsy/Images/master/data/二叉树遍历.png)

实际上，二叉树的前、中、后序遍历就是一个递归的过程

## 遍历代码示例

```java
void preOrder(Node root) {
    if (root == null) return;
    print root // 此处为伪代码，表示打印root节点
    preOrder(root->left);
    preOrder(root->right);
}
void inOrder(Node root) {
    if (root == null) return;
    inOrder(root->left);
    print root // 此处为伪代码，表示打印root节点
    inOrder(root->right);
}

void postOrder(Node* root) {
    if (root == null) return;
    postOrder(root->left);
    postOrder(root->right);
    print root // 此处为伪代码，表示打印root节点
}
```