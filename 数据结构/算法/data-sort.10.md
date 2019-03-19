<!-- ---
title: 基数排序
tags: 
- 基数排序
categories: 
- 算法
--- -->
## 基数排序（Radix sort）

核心思想
- 将整数按位数切割成不同的数字，然后按每个位数分别比较。由于整数也可以表达字符串（比如名字或日期）和特定格式的浮点数，所以基数排序也不是只能使用于整数
- 将所有待比较数值（正整数）统一为同样的数字长度，数字较短的数前面补零。然后，从最低位开始，依次进行一次排序。这样从最低位排序一直到最高位排序完成以后，数列就变成一个有序序列。

![基数排序](https://raw.githubusercontent.com/FameLsy/Images/master/data/基数排序.png)

### 基数排序代码示例

```java
public class RadixSort
{
    public static void sort(int[] number, int d) //d表示最大的数有多少位
    {
        intk = 0;
        intn = 1;
        intm = 1; //控制键值排序依据在哪一位
        int[][]temp = newint[10][number.length]; //数组的第一维表示可能的余数0-9
        int[]order = newint[10]; //数组orderp[i]用来表示该位是i的数的个数
        while(m <= d)
        {
            for(inti = 0; i < number.length; i++)
            {
                intlsd = ((number[i] / n) % 10);
                temp[lsd][order[lsd]] = number[i];
                order[lsd]++;
            }
            for(inti = 0; i < 10; i++)
            {
                if(order[i] != 0)
                    for(intj = 0; j < order[i]; j++)
                    {
                        number[k] = temp[i][j];
                        k++;
                    }
                order[i] = 0;
            }
            n *= 10;
            k = 0;
            m++;
        }
    }
    public static void main(String[] args)
    {
        int[]data =
        {73, 22, 93, 43, 55, 14, 28, 65, 39, 81, 33, 100};
        RadixSort.sort(data, 3);
        for(inti = 0; i < data.length; i++)
        {
            System.out.print(data[i] + "");
        }
    }
}
```
### 基数排序性能分析

空间复杂度分析
- 空间复杂度为O(n+k),非原地排序

稳定性分析
- 稳定算法

时间复杂度分析
- 最好情况,时间复杂度O(kn)
- 最坏情况,时间复杂度O(kn)
- 平均时间复杂度O(kn)

