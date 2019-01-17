---
title: java 基础(3):数组
tags: 
- 数组
categories:
- JavaSe教程
---

Java语言中3个最基本的元素:数据类型,变量和数组

#　一维数组
通用的一维数组的声明格式：
```java
type var-name[ ];
```

数组初始化
```java
//自动初始化值
new type[size];
//自定义初始化值
int[] smallPrimes = { 2, 3, 5, 7, 11, 13 };
```
初始化匿名数组
```java
new int[] { 17, 19, 23, 29, 31, 37 }
```

# 多维数组
```java
int twoD[][] = new int[4][5];
// 也可以,第二维手工分配    
int twoD[][] = new int[4][];
twoD[0] = new int[5];
twoD[1] = new int[5];
twoD[2] = new int[5];
twoD[3] = new int[5];
```
**数组工具类Array**  
![array1](https://raw.githubusercontent.com/FameLsy/Images/master/javase/Array1.png)

![array2](https://raw.githubusercontent.com/FameLsy/Images/master/javase/Array2.png)