---
title: 散列表
tags: 
- 散列表
categories: 
- 数据结构
---

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

```java
nt hash(String key) {
    // 获取后两位字符
    string lastTwoChars = key.substr(length-2, length);
    // 将后两位字符转换为整数
    int hashValue = convert lastTwoChas to int-type;
    return hashValue;
}
```