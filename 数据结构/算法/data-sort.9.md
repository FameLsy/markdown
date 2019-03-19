<!-- ---
title: 计数排序
tags: 
- 计数排序
categories: 
- 算法
--- -->
## 计数排序（Counting sort）

核心思想
- 使用一个额外的数组 C ，其中第i个元素是待排序数组A中值等于 i的元素的个数。
- 然后根据数组 C 来将 A中的元素排到正确的位置

计数排序对数据的要求：
- 计数排序只能用在数据范围不大的场景中，如果数据范围k比要排序的数据n大很多，就不适合用计数排序
- 计数排序只能给非负整数排序，如果要排序的数据是其他类型的，要将其在不改变相对大小的情况下，转化为非负整数。

![计数排序](https://raw.githubusercontent.com/FameLsy/Images/master/data/计数排序.png)


### 计数排序示例代码

算法步骤：
1. 找出待排序的数组中最大和最小的元素
2. 统计数组中每个值为i的元素出现的次数，存入数组C的第i项
3. 对所有的计数累加（从C中的第一个元素开始，每一项和前一项相加）
4. 反向填充目标数组：将每个元素i放在新数组的第C[i]项，每放一个元素就将 C[i]减去1

```java
    public static int[] countingSort(int[] A) {
        int[] B = new int[A.length];
        // 假设A中的数据a'有，0<=a' && a' < k并且k=100
        int k = 100;
        countingSort(A, B, k);
        return B;
    }

    private static void countingSort(int[] A, int[] B, int k) {
        int[] C = new int[k];
        // 计数
        for (int j = 0; j < A.length; j++) {
            int a = A[j];
            C[a] += 1;
        }
        Utils.print(C);
        // 求计数和
        for (int i = 1; i < k; i++) {
            C[i] = C[i] + C[i - 1];
        }
        Utils.print(C);
        // 整理
        for (int j = A.length - 1; j >= 0; j--) {
            int a = A[j];
            B[C[a] - 1] = a;
            C[a] -= 1;
        }
    }
}


//针对c数组的大小，优化过的计数排序
public class CountSort{
	public static void main(String []args){
		//排序的数组
		int a[] = {100, 93, 97, 92, 96, 99, 92, 89, 93, 97, 90, 94, 92, 95};
		int b[] = countSort(a);
		for(int i : b){
			System.out.print(i + "  ");
		}
		System.out.println();
	}
	public static int[] countSort(int []a){
		int b[] = new int[a.length];
		int max = a[0], min = a[0];
		for(int i : a){
			if(i > max){
				max = i;
			}
			if(i < min){
				min = i;
			}
		}
		//这里k的大小是要排序的数组中，元素大小的极值差+1
		int k = max - min + 1;
		int c[] = new int[k];
		for(int i = 0; i < a.length; ++i){
			c[a[i]-min] += 1;//优化过的地方，减小了数组c的大小
		}
		for(int i = 1; i < c.length; ++i){
			c[i] = c[i] + c[i-1];
		}
		for(int i = a.length-1; i >= 0; --i){
			b[--c[a[i]-min]] = a[i];//按存取的方式取出c的元素
		}
		return b;
	}
```
### 计数排序性能分析

空间复杂度分析
- 空间复杂度为O(n),非原地排序

稳定性分析
- 稳定算法

时间复杂度分析
- 最好情况,时间复杂度O(n+k)
- 最坏情况,时间复杂度O(n+k)
- 平均时间复杂度O(n+k)

