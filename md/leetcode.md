---
title: leetcode练习
tags: 
- leetcode
categories: 
- 算法
---

# 两数相加
给出两个 非空 的链表用来表示两个非负的整数。其中，它们各自的位数是按照逆序的方式存储的，并且它们的每个节点只能存储一位数。  

如果，我们将这两个数相加起来，则会返回一个新的链表来表示它们的和。

您可以假设除了数字 0 之外，这两个数都不会以 0 开头。

示例：

>输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)  
>输出：7 -> 0 -> 8  
>原因：342 + 465 = 807  

代码：
```java

/**
 * 伪代码
 * 1. 将当前结点初始化为返回列表的哑结点。
 * 2. 将进位 carry初始化为 0。
 * 3. 将 p 和 q 分别初始化为列表 l1 和 l2 的头部。
 * 4. 遍历列表 l1 和 l2 直至到达它们的尾端。
 *      将 x 设为结点 p 的值。如果 p 已经到达 l1 的末尾，则将其值设置为 0。
 *      将 y 设为结点 q 的值。如果 q 已经到达 l2 的末尾，则将其值设置为 0。
 *      设定 sum = x + y + carry。
 *      更新进位的值，carry = sum / 10。
 *      创建一个数值为 (sum mod 10) 的新结点，并将其设置为当前结点的下一个结点，然后将当前结点前进到下一个结点。
 *      同时，将 p 和 q 前进到下一个结点。
 * 5. 检查 carry = 1是否成立，如果成立，则向返回列表追加一个含有数字 1 的新结点。
 * 6. 返回哑结点的下一个结点。
 */
public class leetcode2 {
    public static void main(String[] args){
        ListNode l1 = new ListNode(2);
        l1.next = new ListNode(4);
        l1.next.next = new ListNode(3);
        ListNode l2 = new ListNode(5);
        l2.next = new ListNode(6);
        l2.next.next = new ListNode(4);
        l1 = addTwoNumbers(l1, l2);
        System.out.println(l1);

    }
    public static ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode dummyHead = new ListNode(0);
        ListNode p = l1, q = l2, curr = dummyHead;
        int carry = 0;
        while (p != null || q != null){
            int x = (p != null) ? p.val : 0;
            int y = (q != null) ? q.val : 0;
            int sum = x + y + carry;
            carry = sum / 10;
            curr.next = new ListNode(sum % 10);
            curr = curr.next;
            if (p != null) p = p.next;
            if (q != null) q = q.next;
        }
        if (carry > 0){
            curr.next = new ListNode(carry);
        }
        return dummyHead.next;

    }

    public static class ListNode {
        int val;
        ListNode next;

        ListNode(int x) {
            val = x;
        }
    }
}
```

复杂度分析

时间复杂度：O(max(m, n))，假设 m 和 n 分别表示 l1 和 l2 的长度，上面的算法最多重复 max(m, n)次。

空间复杂度：O(max(m, n)， 新列表的长度最多为 max(m,n) + 1。

# 无重复字符的最长子串

给定一个字符串，请你找出其中不含有重复字符的 最长子串 的长度。

示例 1:

>输入: "abcabcbb"  
>输出: 3  
>解释: 因为无重复字符的最长子串是 "abc"，所以其长度为3   

示例 2:
 
>输入: "bbbbb"  
>输出: 1  
>解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。  

示例 3:

>输入: "pwwkew"  
>输出: 3  
>解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。  
>请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串(也就是所必须是最短相邻字符！)。

自己的思路：

>1. 设置一个记录最长长度的变量
>2. 设置一个hashset，存放字符
>3. 循环开始，往hashset存入第一个数
>4. 第二个开始，继续存入。判断  
>如果存在，则将hashset长度保存到变量，
>如果不存在，则放入 
>5. 循环结束后，返回长度变量的值

代码：  
解法一：滑动窗口
```java
class Solution {
    public static void main(String[] args){
        int i = lengthOfLongestSubstring3("pwwkew");
    }

    /**
     * 滑动窗口
     *
     * 伪代码：
     * 假设我们设置一个区间 [i, j),（最初 j = ij=i）中。 ans记录长度
     * 然后我们向右侧滑动索引 j，
     *      如果它不在 HashSet 中，我们会继续滑动 j， ans会逐渐增加
     *      直到 s[j] 已经存在于 HashSet中，则开始从i开始将重复的数据知道s[j]不存在(假设s[x]与s[j]相等的，那么s[i]-s[x]可以删除了)
     *
     * 复杂度：O(2n)=O(n),每个字符将被 i 和 j访问两次
     * 空间复杂度：O(min(m,n)),(最大就是整个字符串，最小就是字符集大小m)
     * @param s
     * @return
     */
    public static int lengthOfLongestSubstring(String s) {
        int n = s.length();
        Set<Character> set = new HashSet<>();
        int ans = 0, i = 0, j = 0;
        while (i < n && j < n){
            if(!set.contains(s.charAt(j))){
                set.add(s.charAt(j++));
                ans = Math.max(ans, j - i);

            }else {
                set.remove(s.charAt(i++));
            }
        }
        return ans;
    }
```

解法二：滑动窗口优化（HashMap）
```java
    /**
     * 滑动窗口优化（HashMap）
     *
     * 伪代码：
     * 假设我们设置一个区间 [i, j),（最初 j = i）中
     * 使用HashMap记录字符和索引（索引记录当前 j'+1的位置）
     *      如果在[i, j),有s[j'] 存在重复字符，直接将其实位置i变为 j'+1(丢弃s[i]-s[j']的范围)
     *
     * 复杂度：O(n),索引j迭代n此
     * 空间复杂度：O(min(m,n)),(最大就是整个字符串，最小就是字符集大小m)
     * @param s
     * @return
     */
    public static int lengthOfLongestSubstring2(String s) {
        int n = s.length(), ans = 0;
        Map<Character, Integer> map = new HashMap<>(); // current index of character
        // try to extend the range [i, j]
        for (int j = 0, i = 0; j < n; j++) {
            if (map.containsKey(s.charAt(j))) {
                i = Math.max(map.get(s.charAt(j)), i);
            }
            ans = Math.max(ans, j - i + 1);
            map.put(s.charAt(j), j + 1);
        }
        return ans;
    }
```

解法三：滑动窗口优化（整数数组，用于字符集较小）
```java
    /**
     * 滑动窗口优化（整数数组，用于字符集较小）
     *
     * 常用的表如下所示：
     *      int [26] 用于字母 ‘a’ - ‘z’或 ‘A’ - ‘Z’
     *      int [128] 用于ASCII码
     *      int [256] 用于扩展ASCII
     *
     * 伪代码：
     *      输入字符集 "pwwkew"
     *      如字符集是ASCII码，p->112,w->119,k->107,e->101
     *      把数组int[]看成键值对的话
     *          p->1
     *          w->2
     *      index[s.charAt(j)]起到了类似与hashmap中if语句判断和获取索引的作用
     *      此时与hashmap方法类似
     * 复杂度：O(n),索引j迭代n此
     * 空间复杂度：O(m)
     * @param s
     * @return
     */
    public static int lengthOfLongestSubstring3(String s) {
        int n = s.length(), ans = 0;
        int[] index = new int[128]; // current index of character
        // try to extend the range [i, j]
        for (int j = 0, i = 0; j < n; j++) {
            i = Math.max(index[s.charAt(j)], i);
            ans = Math.max(ans, j - i + 1);
            index[s.charAt(j)] = j + 1;
        }
        return ans;
    }
}
```

# 寻找两个有序数组的中位数

给定两个大小为 m 和 n 的有序数组 nums1 和 nums2。

请你找出这两个有序数组的中位数，并且要求算法的时间复杂度为 O(log(m + n))。

你可以假设 nums1 和 nums2 不会同时为空。

示例 1:

>nums1 = [1, 3]  
>nums2 = [2]  

则中位数是 2.0

示例 2:

>nums1 = [1, 2]  
>nums2 = [3, 4]  

则中位数是 (2 + 3)/2 = 2.5

思路：
> 1. 将两个数组合并
> 2. 使用sort()函数进行排序
> 3. 偶数，取中间值/2
> 4. 奇数，取中间值
> 5. 问题：Array.sort()函数是采用的优化后的快速排序算法，时间复杂度为nlogn(本题为（m+n）log(m+n),显然不符合)

