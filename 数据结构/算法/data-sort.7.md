<!-- ---
title: 快速排序
tags: 
- 快速排序
categories: 
- 算法
--- -->

## 快速排序

核心思想
- 如果要排序数组下标从p到r之间的一组数据
- 首先选择p到r之间的任意一个数据作为pivot（分区点）
- 然后遍历p到r之间的数据，将小于pivot的放到左边，将大于pivot的放到右边，将pivot放到中间
- 最后，根据分治、递归的处理思想，我们可以用递归排序下标从p到q-1之间的数据和下标从q+1到r之间的数据，直到区间缩小为1

![sort5](https://raw.githubusercontent.com/FameLsy/Images/master/data/sort5.png)

### 快速排序示例代码

算法步骤
1. 从数列中挑出一个元素，称为“基准”（pivot），
2. 重新排序数列，所有比基准值小的元素摆放在基准前面，所有比基准值大的元素摆在基准后面（相同的数可以到任何一边）。在这个分割结束之后，该基准就处于数列的中间位置。这个称为分割（partition）操作。
3. 递归地（recursively）把小于基准值元素的子数列和大于基准值元素的子数列排序。

```java
function quick_sort(a[], n)
    quick_sort_c(a[], p, n-1)
end fucntion

function quick_sort_c(a[], p, r)
    if p >= r then return
    // 随机选择一个元素作为pivot（一般情况下，可以选择p到r区间的最后一个元素），然后对A[p…r]分区，函数返回pivot的下标
    q <- partition(a[], p, r)

    quick_sort_c(a[], p, q-1)
    quick_sort_c(a[], q+1, r)
```

但是，为了实现原地排序算法，需要如下设计
```java
function partition(a[], p, r)
    pivot <- a[r]
    i <- p

    for j <- p to r-1 do
        if a[j] < pivot then 
            a[i] <-> a[j]
            i <= i+1
        end if
    
    a[i] <-> a[r]

    return i
```

### 快速排序性能分析

空间复杂度分析
- 空间复杂度为O(1),原地排序

稳定性分析
- 不稳定排序

时间复杂度分析
- 最好情况,时间复杂度O(nlongn)
- 最坏情况,时间复杂度O(n²)
- 平均时间复杂度O(nlongn)
