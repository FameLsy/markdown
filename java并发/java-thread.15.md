<!-- java中的集合 -->

# ConcurrentHashMap

　为什么要使用ConcurrentHashMap？
1. 线程不安全的HashMap会引起死循环
2. 用线程安全的HashTable效率又非常低下（线程都竞争同一把锁）
3. java 1.8中的CurrentHashMap有了较大的不同（以下介绍的是java 7 的版本）

[HashMap会引起死循环](https://juejin.im/post/5a66a08d5188253dc3321da0)

ConcurrentHashMap的锁分段技术
- 数据分段存储，并为每段数据配锁
- 当一个线程占用锁访问其中一个段数据的时候，其他段的数据也能被其他线程访问。

concurrentHashMap类图
1. 由Segment数组结构和HashEntry数组结构组成
2. Segment是一种可重入锁（ReentrantLock）,包含一个HashEntry数组
3. HashEntry则用于存储键值对数据
4. 当对HashEntry数组的数据进行修改时，必须首先获得与它对应的Segment锁

![concurrentHashMap](https://raw.githubusercontent.com/FameLsy/Images/master/thread/concurrentHashMap.png)

## ConcurrentHashMap的初始化

ConcurrentHashMap初始化方法是通过initialCapacity、loadFactor和concurrencyLevel等几个参数来初始化segment数组、段偏移量segmentShift、段掩码segmentMask和每个segment里的HashEntry数组来实现的

### 初始化segments数组、segmentShift和segmentMask

```java
if (concurrencyLevel > MAX_SEGMENTS)
    concurrencyLevel = MAX_SEGMENTS;
int sshift = 0;
//segments长度
int ssize = 1;
//计算出一个大于或等于concurrencyLevel的最小的2的N次方值来作为segments数组的长度 (为了能通过按位与的散列算法来定位segments数组的索引)
while (ssize < concurrencyLevel) {
    ++sshift;//ssize从1向左移位的次数
    ssize <<= 1;
}
//初始化
segmentShift = 32 - sshift;//用于定位参与散列运算的位数（之所以用32是因为ConcurrentHashMap里的hash()方法输出的最大数是32位的）
segmentMask = ssize - 1;//散列运算的掩码（掩码的二进制各个位的值都是1）
this.segments = Segment.newArray(ssize);
```

>注意  
>concurrencyLevel的最大值是65535，这意味着segments数组的长度最大为65536，对应的二进制是16位;所以segmentShift最大值是16，segmentMask最大值是65535，对应的二进制是16位，每个位都是1

### 初始化每个segment

segment的容量 threshold = （int）cap*loadFactor
```java
//initialCapacity是ConcurrentHashMap的初始化容量,默认16 
if (initialCapacity > MAXIMUM_CAPACITY)
    initialCapacity = MAXIMUM_CAPACITY;
int c = initialCapacity / ssize;
if (c * ssize < initialCapacity)
    ++c;
int cap = 1;//变量cap就是segment里HashEntry数组的长度,大小为2^c [c∈(0,n)]
while (cap < c)
    cap <<= 1;
for (int i = 0; i < this.segments.length; ++i)
    this.segments[i] = new Segment<K,V>(cap, loadFactor);//，loadfactor是每个segment的负载因子，默认0.75
```


## 定位Segment

然ConcurrentHashMap在插入和获取元素的时候，必须先通过散列算法定位到Segment,才能使用不同段的数据

到ConcurrentHashMap会首先使用Wang/Jenkins hash的变种算法对元素的hashCode进行一次再散列

```java
private static int hash(int h) {
    h += (h << 15) ^ 0xffffcd7d;
    h ^= (h >>> 10);
    h += (h << 3);
    h ^= (h >>> 6);
    h += (h << 2) + (h << 14);
    return h ^ (h >>> 16);
}
```

ConcurrentHashMap通过以下散列算法定位segment
```java
//默认情况下segmentShift为28，segmentMask为15，再散列后的数最大是32位二进制数据，向右无符号移动28位，意思是让高4位参与到散列运算中
final Segment<K,V> segmentFor(int hash) {
    return segments[(hash >>> segmentShift) & segmentMask];
}
```

此外，定位HashEntry所使用的hash算法如下
```java
int index = hash & (tab.length - 1);　
```


## ConcurrentHashMap的操作 

ConcurrentHashMap的3种操作
1. get操作
2. put操作
3. size操作

### get操作

get操作的高效之处在于整个get过程不需要加锁，除非读到的值是空才会加锁重读

```java
public V get(Object key) {
    //先进行一次再散列
    int hash = hash(key.hashCode());
    //再使用使用这个散列值通过散列运算定位到Segment，再通过散列算法定位到元素
    return segmentFor(hash).get(key, hash);
}
```

为什么get方法不用加锁？
- 是它的get方法里将要使用的共享变量都定义成了volatile类型
- 根据ava内存模型的happen before原则，对volatile字段的写入操作先于读操作


### put 操作

put方法里需要对共享变量进行写入操作，在操作共享变量时必须加锁，其逻辑如下
1. 首先定位到Segment，然后在Segment里进行插入操作
2. 第一步判断是否需要对Segment里的HashEntry数组进行扩容(在插入元素前会先判断Segment里的HashEntry数组是否超过容量（threshold）)
3. 第二步定位添加元素的位置，然后将其放在HashEntry数组里

如何扩容?
- 创建一个容量是原来容量两倍的数组，然后将原数组里的元素进行再散列后插入到新的数组里(对Segment的扩容)

### size操作

要计算CurrentHashMap的大小，需要将Segment里的全局变量count累计相加，count是一个volatile变量，在获取时将得到最新的数据，但在计算相加前可能发生改变。

ConcurrentHashMap的做法如下
- 先尝试2次通过不锁住Segment的方式来统计各个Segment大小
- 如果果统计的过程中，容器的count发生了变化，则再采用加锁的方式来统计所有Segment的大小
- 通过使用modCount变量，在put、remove和clean方法里操作元素前都会将变量modCount进行加1，在统计size前后比较modCount是否发生变化，从而得知容器的大小是否发生变化


# ConcurrentLinkedQueue

实现一个线程安全的队列有两种方式：
- 使用阻塞算法（可以用一个锁（入队和出队用同一把锁）或两个锁（入队和出队用不同的锁）等方式来实现）
- 使用非阻塞算法(用循环CAS的方式来实现)

ConcurrentLinkedQueue
- 基于链接节点的无界线程安全队列
- FIFO规则排序
- 采用了“wait-free”算法（即CAS算法）来实现

concurrentLinkedQueue类图
- 由head节点和tail节点组成
- 每个节点（Node）由节点元素（item）和指向下一个节点（next）的引用组成
- 默认情况下head节点存储的元素为空，tail节点等于head节点

![concurrentLinkedQueue](https://raw.githubusercontent.com/FameLsy/Images/master/thread/concurrentLinkedQueue.png)

## 入队列


首先要理解入队操作(java入队操作与数据结构算法中的有些不同)
1. 一个空的队列，插入节点A，Head.next设置为节点A，tail默认与head节点相同，也指向A
2. 添加第二个节点B,节点A.next设置为节点B，同时更新tail指向节点B
3. 添加第三个节点C，节点B.next设置为节点C
4. 添加第四个节点D，节点C.next设置为节点D，同时更新tail指向节点D
5. 可以看出，tail节点有可能不是尾节点(重点)

个入队过程逻辑如下
1. 定位出尾节点(tail节点或者tail.next是尾节点)
2. 使用CAS算法将入队节点设置成尾节点的next节点，如不成功则重试
```java
public boolean offer(E e) {
        if (e == null) throw new NullPointerException();
        // 入队前，创建一个入队节点
        Node<E> n = new Node<E>(e);
        retry:
        // 死循环，入队不成功反复入队。
        for (;;) {
        // 创建一个指向tail节点的引用
            Node<E> t = tail;
        // p用来表示队列的尾节点，默认情况下等于tail节点。
            Node<E> p = t;
            for (int hops = 0; ; hops++) {
        // 获得p节点的下一个节点。
                Node<E> next = succ(p);
        // next节点不为空，说明p不是尾节点，需要更新p后在将它指向next节点
                if (next != null) {
        // 循环了两次及其以上，并且当前节点还是不等于尾节点
                    if (hops > HOPS && t != tail)
                        continue retry;
                    p = next;
                }
        // 如果p是尾节点，则设置p节点的next节点为入队节点。
                else if (p.casNext(null, n)) {
        /*如果tail节点有大于等于1个next节点，则将入队节点设置成tail节点，更新失败了也没关系，因为失败了表示有其他线程成功更新了tail节点*/
                    if (hops >= HOPS)
                        casTail(t, n); // 更新tail节点，允许失败
                    return true;
                }
        // p有next节点,表示p的next节点是尾节点，则重新设置p节点
                else {
                    p = succ(p);
                }
            }
        }
    }
```

为什么ConcurrentLinkedQueue入队不是每次都变化tail节点?
1. 减少CAS更新tail节点的次数，能提高入队的效率
2. ConcurrentLinkedQueue使用hops变量来控制并减少tail节点的更新频率
3. hops变量相当于tail与尾节点的长度，如果大于等于HOPS(默认1)，才更新节点
4. tail与尾节点越长，那么更新tail节点操作就越少，但是相对的每次定位到尾节点需要读取更多的节点（相当于通过添加对volatile变量的读操作减少写操作，而读操作开销要远远小于写操作，从而得到提升）

> 注意  
> 入队方法永远返回true，所以不能靠返回值判断是否入队成功

## 出队列

逻辑如下
1. 与入队列相同，通过hops来减少更新head节点操作
2. head 节点内有元素，则弹出元素
3. head节点为空，弹出head.next,更新head节点

```java
 public E poll() {
        Node<E> h = head;
// p表示头节点，需要出队的节点
        Node<E> p = h;
        for (int hops = 0; ; hops++) {
// 获取p节点的元素
            E item = p.getItem();
// 如果p节点的元素不为空，使用CAS设置p节点引用的元素为null,
// 如果成功则返回p节点的元素。
            if (item != null && p.casItem(item, null)) {
                if (hops >= HOPS) {
// 将p节点下一个节点设置成head节点
                    Node<E> q = p.getNext();
                    updateHead(h, (q != null)q :p);
                }
                return item;
            }
// 如果头节点的元素为空或头节点发生了变化，这说明头节点已经被另外
// 一个线程修改了。那么获取p节点的下一个节点
            Node<E> next = succ(p);
// 如果p的下一个节点也为空，说明这个队列已经空了
            if (next == null) {
// 更新头节点。
                updateHead(h, p);
                break;
            }
// 如果下一个元素不为空，则将头节点的下一个节点设置成头节点
            p = next;
        }
        return null;
    }
```