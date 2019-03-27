<!-- 垃圾收集器 -->
相关概念补充：
- 并行（Parallel） ：指多条垃圾收集线程并行工作，但此时用户线程仍然处于等待状态。
- 并发（Concurrent）：指用户线程与垃圾收集线程同时执行（但不一定是并行，可能会交替执行），用户程序在继续运行，而垃圾收集器运行在另一个CPU上。
- 吞吐量：COU用于运行用户代码的时间与CPU总消耗时间的比值，即吞吐量=运行用户代码时间/(运行用于代码时间+垃圾回收时间)

# HotSpot 提供的收集器

收集器的连线表示它们可以配合使用

![jvm7](https://raw.githubusercontent.com/FameLsy/Images/master/javamode/jvm7.png)

# Serial 收集器

Serial收集器是一个 *单线程收集器* ,它只会使用一条垃圾收集线程去完成垃圾收集工作，它在进行垃圾收集工作的时候必须暂停其他所有的工作线程（有一种叫法，叫 *Stop The World* ），直到它收集结束;它时JVM运行在 *Client模式* 下的默认 *新生代收集器* ,它的优点在于简单高效，尤其是单个CPU环境。Serial收集器由于没有线程交互的开销，自然可以获得很高的单线程收集效率

![jvm8](https://raw.githubusercontent.com/FameLsy/Images/master/javamode/jvm8.png)

# ParNew 收集器

ParNew收集器其实就是Serial收集器的并行多线程版本,其余行为与Serial收集器相同；是运行在Server模式下的JVM的首选收集器，有一个重要的原因是它能与COMS收集器配置。

![jvm9](https://raw.githubusercontent.com/FameLsy/Images/master/javamode/jvm9.png)


# Parallel Scsvenge收集器

Parallel Scsvenge收集器是一个 *新生代收集器*没使用的是 *复制算法*,同时也是并行的多线程收集器,与ParNew 收集器几乎一直，特点时它的关注点与其他收集器不同；其目标是达到一个可控制的吞吐量。它提供了两个参数用于精确控制吞吐量
1. -XX:MaxGCPauseMillis 控制最大垃圾收集停顿时间(大于0的毫秒数)，收集器尽可能保证内存回收花费时间不超过设定值，时间缩短靠的是牺牲吞吐量和新生代空间换取的
2. -XX:GCTimeRatio 直接设置大小（大于0小于100，垃圾手机时间占总时间的比率，即吞吐量的倒数）,如设置成19，那么GC时间就是 1/(1+19)= 5%,默认值99，即GC时间1%(1/(1+99))

与ParNew收集器的另一个区别是 *GC自适应的调节策略*,这跟Parallel Scsvenge收集器的另一个参数 *-XX:+UserAdaptiveSizePolicy*有关，这是一个开复安参数，打开后，无需手工指定新生代大小、Eden与Survior的比例等细节参数，JVM会自动调节。 
