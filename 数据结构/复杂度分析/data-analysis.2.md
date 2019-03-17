---
title: 时间复杂度
tags: 
- 时间复杂度
categories: 
- 复杂度分析
---

# 时间复杂度分析

时间复杂度的分析，主要由三种方法
1. 只关注循环执行次数最多的一段代码
2. 加法法则：总复杂度等于量级最大的那段代码的复杂度
3. 乘法法则：嵌套代码的复杂度等于嵌套内外代码复杂度的乘积

# 几种常见时间复杂度分析


![time-complexity](https://raw.githubusercontent.com/FameLsy/Images/master/data/time-complexity.png)

## O(1)

常量级时间复杂度的一种表示方法
```java
int i = 8;
int j = 6;
int sum = i + j;
```

## O(logn)\O(nlogn)

变量i的取值就是一个等比数列,得到n = logn(不管以谁为底，都可以变为logn;)

![time-complexity2](https://raw.githubusercontent.com/FameLsy/Images/master/data/time-complexity2.png)

```java
i=1;
while (i <= n) {
    i = i * 2;
}
```

O(nlogn)就是嵌套在了一个O(n)代码块中(乘法法则)

## O(m+n)、O(m*n)

无法确定m和n的规模，所以无法使用加法法则，省略其中一个，所以此时为O(m+n)

```java
//循环1
for (; i < m; ++i) {
    sum_1 = sum_1 + i;
}
//循环2
for (; j < n; ++j) {
    sum_2 = sum_2 + j;
}
```

但是m, n的无论什么规模适用于乘法法则，即O(m*n)

## 时间复杂度增长趋势图

![time-complexity3](https://raw.githubusercontent.com/FameLsy/Images/master/data/time-complexity3.png)

## 最好、最坏、平均、均摊时间复杂度

最好情况时间复杂度（best case time complexity）:即当执行最少次数时的复杂度

最坏情况时间复杂度（worst case time complexity）：即当执行最多次数时的复杂度

平均情况时间复杂度（average case time complexity）： 以查找变量x在大小为n的数组的位置为例;
- 要查找的变量x在数组中的位置，有n+1种情况：在数组的0～n-1位置中和不在数组中
- 假设在数组中与不在数组中的概率都为1/2
- 查找的数据出现在0～n-1这n个位置的概率即为1/n
- 将每种情况下的找对位置所需遍历的元素数目和到的概率相乘，并累加，就可以得到一个平均时间复杂度
- (实际上这个值就是概率学中的加权平均值)

![average](https://raw.githubusercontent.com/FameLsy/Images/master/data/average.png)

均摊时间复杂度（amortized time complexity）：应用场景极为特殊，不常见

如下代码，只有在array满的时候，即代码中的count == array.length时，时间复杂度为O(n)，其余复杂度都为O(1)

```java
// array表示一个长度为n的数组
// 代码中的array.length就等于n
int[] array = new int[n];
int count = 0;
void insert(int val) {
    if (count == array.length) {
        int sum = 0;
        for (int i = 0; i < array.length; ++i) {
            sum = sum + array[i];
        }
    array[0] = sum;
    count = 1;
    }   
    array[count] = val;
    ++count;
}
```

对于这种特殊的场景引入了一种更加简单的分析方法：摊还分析法

摊还分析法：每一次O(n)的插入操作，都会跟着n-1次O(1)的插入操作，所以把耗时多的那次操作均摊到接下来的n-1次耗时少的
操作上，均摊下来，这一组连续的操作的均摊时间复杂度就是O(1)。

![amortized](https://raw.githubusercontent.com/FameLsy/Images/master/data/amortized.png)

对于均摊时间复杂度和摊还分析应用场景：对一个数据结构进行一组连续操作中，大部分情况下时间复杂度都很低，只有个别情况下时间复杂度比较高，如果能将较高时间复杂度那次操作的耗时，平摊到其他那些时间复杂度比较低的操作上（一般均摊时间复杂度就等于最好情况时间复杂度）


# 通过有序度和逆序度的概念来分析平均时间复杂度

有序元素对( a[i]<=a[j],i< j )


![sort3](https://raw.githubusercontent.com/FameLsy/Images/master/data/sort2.png)
 

同理，"6,5,4,3,2,1"的有序度为0；而"1,2,3,4,5"的有序度为 "n*(n-1)/2",也叫做“满有序度”


可以得到如下公式
```java
逆序度 = 满有序度-有序度
```

每次交换，相当于有序度加1，逆序度就是跟有序度相反，相当于交换次数。

如果有序度为0，那么就相当于要交换n*(n-1)/2,取中间值n*(n-1)/4,也就是平均需要n*(n-1)/4此交换，那么，平均复杂度可以粗略的认为是O(n^2)

# 如何分析递归函数的复杂度

归并排序涉及递归，时间复杂度的分析稍微有点复杂。我们正好借此机会来学习一下，如何分析递归代码的时间复杂度。
在递归那一节我们讲过，递归的适用场景是，一个问题a可以分解为多个子问题b、c，那求解问题a就可以分解为求解问题b、c。问题b、c解决之后，我们再
把b、c的结果合并成a的结果。
如果我们定义求解问题a的时间是T(a)，求解问题b、c的时间分别是T(b)和 T( c)，那我们就可以得到这样的递推关系式：
T(a) = T(b) + T(c) + K
其中K等于将两个子问题b、c的结果合并成问题a的结果所消耗的时间。
从刚刚的分析，我们可以得到一个重要的结论：不仅递归求解的问题可以写成递推公式，递归代码的时间复杂度也可以写成递推公式。
套用这个公式，我们来分析一下归并排序的时间复杂度。
我们假设对n个元素进行归并排序需要的时间是T(n)，那分解成两个子数组排序的时间都是T(n/2)。我们知道，merge()函数合并两个有序子数组的时间复杂度
是O(n)。所以，套用前面的公式，归并排序的时间复杂度的计算公式就是：
T(1) = C； n=1时，只需要常量级的执行时间，所以表示为C。
T(n) = 2*T(n/2) + n； n>1
通过这个公式，如何来求解T(n)呢？还不够直观？那我们再进一步分解一下计算过程。
T(n) = 2*T(n/2) + n
= 2*(2*T(n/4) + n/2) + n = 4*T(n/4) + 2*n
= 4*(2*T(n/8) + n/4) + 2*n = 8*T(n/8) + 3*n
= 8*(2*T(n/16) + n/8) + 3*n = 16*T(n/16) + 4*n
......
= 2^k * T(n/2^k) + k * n
12|排序（下）：如何用快排思想在O(n)内查找第K大元素？
file:///F/temp/geektime/数据结构与算法之美/12排序（下）：如何用快排思想在O(n)内查找第K大元素？.html[2019/1/15 15:35:29]
......
通过这样一步一步分解推导，我们可以得到T(n) = 2^kT(n/2^k)+kn。当T(n/2^k)=T(1)时，也就是n/2^k=1，我们得到k=log2n 。我们将k值代入上面的公式，得
到T(n)=Cn+nlog2n 。如果我们用大O标记法来表示的话，T(n)就等于O(nlogn)。所以归并排序的时间复杂度是O(nlogn)。
从我们的原理分析和伪代码可以看出，归并排序的执行效率与要排序的原始数组的有序程度无关，所以其时间复杂度是非常稳定的，不管是最好情况、最坏情
况，还是平均情况，时间复杂度都是O(nlogn)。