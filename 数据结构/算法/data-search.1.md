<!-- ---
title: 二分查找
tags: 
- 二分查找
categories: 
- 算法
--- -->

# 二分查找

又被称为折半查找算法

核心思想
- 每次都通过跟区间的中间元素对比，将待查找的区间缩小为之前的一半，直到找到要查找的元素，或者区间被缩小为0。

二分查找应用场景的局限性
1. 二分查找依赖的是顺序表结构，简单点说就是数组
2. 二分查找针对的是有序数
3. 二分查找只能用在插入、删除操作不频繁，一次排序多次查找的场景中。针对动态变化的数据集合，二分查找将不再适用
4. 要处理的数据量很小，完全没有必要用二分查找,直接遍历即可
5. 数据之间的比较操作非常耗时，不管数据量大小，推荐使用二分查找(二分查找比较次数大大减少)
6. 数据量太大也不适合二分查找(二分查找依赖数组，且必须是连续空间)


![二分查找](https://raw.githubusercontent.com/FameLsy/Images/master/data/二分查找.png)

## 二分查找示例代码

算法步骤
1. 令L为0,R为n-1。
2. 如果L>R，则搜索以失败告终。
3. m（中间值元素）为(L+R)/2。（具体实现中，为防止算术溢出，一般采用L+(R-L)/2代替。）
4. 如果A[m]<T，令L为m+1,并回到步骤二。（T表示需要查找的值）
5. 如果A[m]>T, 令R为m-1并回到步骤二。
6. 当A[m]=T, 搜索结束；回传值m

递归实现：
```java
public static int binarySearch(int[] arr, int start, int end, int hkey){
    if (start > end)
        return -1;

    int mid = start + (end - start)/2;    //防止溢位
    if (arr[mid] > hkey)
        return binarySearch(arr, start, mid - 1, hkey);
    if (arr[mid] < hkey)
        return binarySearch(arr, mid + 1, end, hkey);
    return mid;  

}
```

循环实现
```java
```Java

public static int binarySearch(int[] arr, int start, int end, int hkey){
    int result = -1;

    while (start <= end){
        int mid = start + (end - start)/2;    //防止溢位
        if (arr[mid] > hkey)
            end = mid - 1;
        else if (arr[mid] < hkey)
            start = mid + 1;
        else {
            result = mid ;  
            break;
        }
    }

    return result;

}
```

## 二分查找性能分析

空间复杂度分析
- 迭代: O(1)
- 递归：O(log n)

稳定性分析
- 不涉及交换，稳定

时间复杂度分析
- 最好情况,时间复杂度O(1)
- 最坏情况,时间复杂度O(logn)
- 平均时间复杂度,O(logn)
