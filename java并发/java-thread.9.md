<!-- ConcurrentLinkedQueue -->

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

# 入队列


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

# 出队列

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