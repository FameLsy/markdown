<!-- 垃圾回收算法 -->

# 标记-清除算法(Mark-Sweep)

算法分为 *标记* 和 *清除*两个节点
1. 标记处所有需要回收的对象(标记过程看 ”可达性分析算法" 这一章的内容)
2. 标记完成后统一回收

该算法有两处不足
1. 效率低，无论是标记还是清除效率都不高
2. 空间问题，清除后会产生大量不连续内存碎片

![jvm4](https://raw.githubusercontent.com/FameLsy/Images/master/javamode/jvm4.png)

# 复制算法(Copying)

算法步骤
1. 将内存分为大小相等的两块
2. 每次使用其中一块，当被使用的内存块用玩了，则将该内存块中存货对象复制到另一块上，然后一次性清空该块。

![jvm5](https://raw.githubusercontent.com/FameLsy/Images/master/javamode/jvm5.png)

如果1：1分的话，有点浪费内存;实际上大多数对象都是很快被回收，所以可以将内存分为一块较大的 *Eden* 空间和两块较小的 *Survivor* 空间；使用 *Eden* 空间和其中一块 *Survivor* 。回首时，将 *Eden* 和 *Survivor* 复制到另一块Survivor中，然后一次性清理。HotSpot默认的比例为8:1:1,也就是说内存空间的使用率为90%（新生代）;当 *Survivor* 内存不足时，需要依赖其他内存（老年代）进行分配担保


# 标记整理法

标记整理法是针对老年代的算法，它的步骤与 *标记清除法* 类似
1. 标记处所有需要回收的对象(标记过程看 ”可达性分析算法" 这一章的内容)
2. 所有存活对象移动到一端，然后直接清理端边界意外的内存

![jvm6](https://raw.githubusercontent.com/FameLsy/Images/master/javamode/jvm6.png)

# 分代收集算法（Generational Collection）

该算法是根据对象存货周期的不同将内存划几块，一般将Java堆分为 *新生代* 和 *老年代*,然后根据各个年代的特点选择不同的算法
1. 新生代对象存活少，就选用复制算法
2. 老年代存活率高，没有额外空间对它进行分配担保，则必须使用 *标记清除法* 或 *标记整理法* 