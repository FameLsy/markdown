---
title: 选择排序
tags: 
- 选择排序
categories: 
- 算法
---

## 选择排序

特点:
- 将数组分为两个区间，已排序区间和未排区间
- 已排序区间初始没有元素，数组全为未排序区间
- 核心思想为从未排序区间取最小元素，然后将其放到已经排序的末尾，保证已排序区间一直有序，直到未排序区间元素为空

插入排序优化：希尔排序

### 选择排序示例代码

算法步骤
1. 一开始，所有数据都处于未排序区间C[i]
2. 设定一个minIndex记录最小值坐标
3. 如果元素小于C[minIndex],就将该元素的坐标赋值给minIndex
4. 完成便利后将C[minIndex]与C[i]的值进行交换

```java
function selectionSort(a[], n)
    if n <=1 return

    for i ∈ [0, n-1) do
        //最小位置
        minIndex <- i
        //遍历出最小值
        for j ∈[i+1, n) do
            if a[j] < a[minIndex] then
                minIndex = j
            end if
        end for 

        //最小值放入尾部
        a[i] <-> a[minIndex]
    end for

end function
```

### 选择排序性能分析

空间复杂度分析
- O(1),原地排序算法

稳定性分析
- 不稳定

时间复杂度分析
- 最好情况,时间复杂度O(n)
- 最坏情况,时间复杂度O(n²)
- 平均时间复杂度,O(n²)
