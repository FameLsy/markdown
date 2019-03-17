---
title: 栈
tags: 
- 栈
categories: 
- 数据结构
---

## 栈

什么是栈？
- 栈是一种“操作受限”的线性表，只允许在一端插入和删除数据
- 栈的操作主要为入栈和出栈

栈的复杂度
- 在入栈和出栈过程中，只需要一两个临时变量存储空间，所以空间复杂度为O(1)
- 入栈、出栈是对栈顶个别数据操作，时间复杂度也是O(1)

## 简单的栈代码

```java

// 基于数组实现的顺序栈
public class ArrayStack {
    private String[] items; // 数组
    private int count; // 栈中元素个数
    private int n; //栈的大小

    // 初始化数组，申请一个大小为n的数组空间
    public ArrayStack(int n) {
        this.items = new String[n];
        this.n = n;
        this.count = 0;
    }

    // 入栈操作
    public boolean push(String item) {
// 数组空间不够了，直接返回false，入栈失败。
        if (count == n) {
            return false;
        }
// 将item放到下标为count的位置，并且count加一
        items[count] = item;
        ++count;
        return true;
    }

    // 出栈操作
    public String pop() {
// 栈为空，则直接返回null
        if (count == 0) return null;
// 返回下标为count-1的数组元素，并且栈中元素个数count减一
        String tmp = items[count - 1];
        --count;
        return tmp;
    }
}
```

## 栈的应用

### 栈在函数调用中的应用

比较经典的一个应用场景：函数调用栈

操作系统给每个线程分配了一块独立的内存空间，这块内存被组织成“栈”这种结构,用来存储函数调用时的临时变量。每进入一个函数，就会将临时变
量作为一个栈帧入栈，当被调用函数执行完成，返回之后，将这个函数对应的栈帧出栈

假设如下方法
```java
int main() {
    int a = 1;
    int ret = 0;
    int res = 0;
    ret = add(3, 5);
    res = a + ret;
    printf("%d", res);
    reuturn 0;
}
int add(int x, int y) {
    int sum = 0;
    sum = x + y;
    return sum;
}
```

其内存情况如下图

![stack](https://raw.githubusercontent.com/FameLsy/Images/master/data/stack.png)

### 栈在表达式求值中的应用

栈的另一种常见应用场景，编译器通过栈来实现表达式求值

编译器通过两个栈来实现表达式求值
- 其中一个保存操作数的栈，另一个是保存运算符的栈。
- 编译器从左向右遍历表达式，当遇到数字，就直接压入操作数栈；
- 当遇到运算符，就与运算符栈的栈顶元素进行比较。
- 如果比运算符栈顶元素的优先级高，就将当前运算符压入栈；
- 如果比运算符栈顶元素的优先级低或者相同，从运算符栈中取栈顶运算符，从操作数栈的栈顶取2个操作数，然后进行计算，再把计算完的结果压入操作数栈，继续比较。

如：3+5*8-6的计算过程图如下

![stack2](https://raw.githubusercontent.com/FameLsy/Images/master/data/stack2.png)

### 栈在括号匹配中的应用

可以借助栈来检查表达式中的括号是否匹配

如表达式中含有:"()"、"{}"、"[]"
- ({[]}):像这种就是合法的
- }){]([)：这样的就是不合法的

那么如何用栈解决
- 从左到右一次扫描
- 扫描到左括号时，存入栈中
- 烧苗到右括号时，从栈顶去除一个左括号，如果匹配，则继续扫描；如果不匹配或者没有数据时，说明为非法格式
