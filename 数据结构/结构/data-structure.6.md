<!-- ---
title: 散列表
tags: 
- 散列表
categories: 
- 数据结构
--- -->

# 散列表(HashTable)

什么是散列表?
- key-value形式存储数据
- 通过散列函数将key转换成value(Hash值)
- 散列函数把元素的key映射为下标，然后将value存储在数组中对应下标的位置


![hashtable](https://raw.githubusercontent.com/FameLsy/Images/master/data/hashtable.png)


## 散列函数

散列函数设计的基本要求：
1. 散列函数计算得到的散列值是一个非负整数
2. 如果key1 = key2，那hash(key1) == hash(key2)
3. 如果key1 ≠ key2，那hash(key1) ≠ hash(key2)（不可能实现，无法避免散列冲突）


## 散列冲突

常用的散列冲突解决方法有两类
1. 开放寻址法（open addressing）
2. 链表法（chaining）

装载因子：用装载因子（load factor）来表示空位的多少
- 散列表的装载因子=填入表中的元素个数/散列表的长度
- 装载因子越大，说明空闲位置越少，冲突越多，散列表的性能会下降

### 开放寻址法

核心思想：
- 出现了散列冲突，就重新探测一个空闲位置，将其插入

线性探测（Linear Probing）
- 插入：当存储位置被占用，从当前位置开始依次查找，直到找到空闲位置
- 删除：对于删除一个数据，特殊标记为deleted（为了防止本来应该存在的数据，因为删除后有了空闲位置而不继续查找）
- 查找：从当前位置开始依次查找，知道空闲位置说明不存在，如果遇到deleted标记，继续向下查找


![开放寻址](https://raw.githubusercontent.com/FameLsy/Images/master/data/开放寻址.png)


二次探测（Quadratic probing)
- 与线性探测类似
- 区别是线性探测每次探测的步长是1，探测的下标序列就是hash(key)+0，hash(key)+1，hash(key)+2； 而二次探测探测的步长就变成了原来的“二次方”，探测的下标序列就是hash(key)+0，hash(key)+1²，hash(key)+2²

双重散列（Double hashing）
- 使用一组散列函数hash1(key)，hash2(key)，hash3(key)
- 第一个散列函数，如果计算得到的存储位置已经被占用，再用第二个散列函数，依次类推，直到找到空闲的存储位置。



### 链表法

核心思想：
- 在散列表中，每个“桶（bucket）”或者“槽（slot）”会对应一条链表，所有散列值相同的元素我们都放到相同槽位对应的链表中
- 插入：通过散列函数计算出对应的散列槽位，将其插入到对应链表中即可（O(n)）
- 查找、删除：通过散列函数计算出对应的槽，然后遍历链表查找或者删除（O(k),k为链表长度）

![链表法](https://raw.githubusercontent.com/FameLsy/Images/master/data/链表法.png)