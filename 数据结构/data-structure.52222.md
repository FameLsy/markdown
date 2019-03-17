---
title: 树(Tree)
tags: 
- 数据结构
categories: 
- 数据结构
---


# 查找

查找（Searching）
- 根据给定关键字K，从集合R中找出关键字和K相同的记录。
- 静态查找：集合中记录固定，没有插入和删除操作，只有查找
- 动态查找：集合中记录是动态变化的，除查找，还可能发生插入和删除

# 静态查找：

方法1：顺序查找 (T(n) = O(n))
```java
public class LNode<E> {
    E[] elementData;

    /**
    * 
    * @param element
    * @return element的位置
    */
    public int sequentialSearch(E element){
        int i;
        for (i = elementData.length;i > 0 && elementData[i] != element; i-- );
        return i;
    }
}
```

技巧，建立哨兵
- 以查找数组为例。建立哨兵后，for循环减少了一个判断条件。
- 注：elementData 数组必须要从1开始存储

```java
/**
*
* @param element
* @return element的位置,0 则未找到
*/
public int sequentialSearch2(E element){
    int i;
    elementData[0] = element;//建立哨兵
    for (i = elementData.length;i > 0 && elementData[i] != element; i-- );//即使没有找到，0的位置肯定是element（哨兵的值就是这个）
    return i; //i如果是0就是没找到
}
```

方法2：二分查找（Binary Search）

T（n） = O(logN), 数组需要按顺序存储。
```java
int[] numbers;

public int binarySearch(int number){
    int left = 0; //设置左边界
    int right = elementData.length-1;//设置有边界
    int mid;//中心
    while (left <= right){
        mid = (left + right)/2;
        if (number < numbers[mid]) right = mid -1; //调整右边界
        else if(number > numbers[mid]) left = mid + 1;//调整左边界
        else return mid;
    }
    return -1;//-1表示没找到
}
```
很明显，方法2 在查找速度上具有更好的性能。

其实，假设有11个数，计算每次mid的值，可以得到如下图，中间数字代表mid的值，往左表示值小于mid，往右表示值大于mid

如当element < elementData[5]时，接下来要比较的就是(0+5-1)/2 = 2,也就是elementData[2]这个分支

如此类推成树状的结构。