---
title: java基础(13)：lambda表达式
date: 2018-02-02 00:00:13
tgas: 
- lambda表达式
categories: 
- JavaSe教程
---

# lambda表达式
lambda表达式：可传递的代码块  
语法：参数、->、表达式(无需指定返回类型)
```java
(String first, String second)
    -> first.length() - second.length()
```

**Lambda表达式省略**  

没有参数时：
```java
()-> {
    ...
} 
```
可以推导出参数类型时，可省略参数类型
```java
(first, second)->{
    first.length() - second.length()
} 
```

只有一个参数，且可以推导类型，可省略 *()*
```java
event 
    -> System.out.print("This time is" + new Date())
```

# 函数式接口

函数式接口：只有一个抽象方法的接口,需要这种接口的对象时，可以提供lambda表达式。  
如:  
Arrays.sort()这个方法，它的第二个参数是一个Comparator接口.
```java
Arrays.sort(T[] a, Comparator<? super T> c)
```
然而在Comparator接口中，发现了两个抽象方法和一个注释  
```java
@FunctionalInterface //表明其为函数式接口
public interface Comparator<T> {
    int compare(T o1, T o2);
    boolean equals(Object obj);
    ...
}
```

原因：函数式接口还可以定义Object的方法，即equals属于Object方法，所以Comparator属于函数式接口

实例：
```java
Arrays.sort(words, 
    (first, second)->first.length() - second.length());
```

在底层，Arrays.sort接受的是Comparator< String >这个对象,在这个对象调用compare方法时会执行lambda表达式。

# 方法引用
将现成的方法传递到其他代码中  
如
```java
Timer t = new Timer(1000, event -> System.out.println(event));
```
可以直接将println方法传递到Timer构造器
```java
//System.out::println等同于 x -> System.out.println(x)
Timer t = new Timer(1000,System.out::println);
```

对于使用 *::* ，一共有三种情况  
1. object::instanceMethod
2. Class::staticMethod
3. Class::instanceMethod

前2种情况中,方法引用等价于提供方法参数的lambda 表达式(Math::pow等价于(x,y)->Math.pow(x,y))  
第3种情况 , 第 1 个参数会成为方法的目标 。(String::compareTolgnoreCase等价于(x,y)->x.compareTOlgnoreCase(y))

>注  
>因为是将现有的方法传递到代码中，有可能出现重载方法。如Math.max有两个版本，一个用于整数，一个用于double  
>选择哪一个版本取决于Math::max转换成哪一个函数式接口的方法参数

可以在方法中引用this和super
```java
this::equals
//等同于
x -> this.equals(x)
```

# 构造器引用
类似于方法引用，但方法名为new(可以理解为特殊的方法引用)  
调用的构造器取决于上下文
```java
int[]::new 
//等价于
x -> new int[x]
```

**一个构造器的应用**：  
在泛型中，是无法构造泛型数组的
```java
new T[n] //error
```
利用构造器引用，可以解决这个问题
```java
//在流中，可以使用toArray传入想要的数据类型来获取想要的数组（以获取Peron[]数组为例）
Person[] people = aStream.toArray(Person[]::new);
```

# lambda变量作用域
```java
// ActionListener 是一个函数是接口，所以lambda表达式可以赋值给它
pubic static void repeatMessage(String text, int delay){
    ActionListener aListener = event ->{
        System.out.println(text);
    }
    new Timer(delay, aListener).start();
}

//调用
repeatMessage("hello", 1000);//print hello
```
如上代码，lambda表达式使用了外部的变量text,而真正执行lambda，可能要等到定时器运行为止，此时：text变量可能已经不存在了。

那么lambda是如何保存变量的呢？  
答：首先看lambda表达式的组成
1. 一个代码块
2. 参数
3. 自有变量（即非参数且不在代码块中定义的，这里就是text）

自有变量的值，可以被lambda表达式捕获。具体细节为：lambda表达式最终会变为一个包含方法的实例对象，而自由变量的值会复制到这个对象的实例变量中

>注: 
>代码块和自有变量值有一个术语：闭包

## lambda捕获的限制

限制一：不能在lambda表达式中改变捕获的自由变量的值
```java
public void static countDonw(int start, int end){
    ActionListener aListener = event ->{
        start--;
        System.out.println(start);//Error，不能改变捕获的变量
    }
    new Timer(delay, aListener).start();
}
```

限制二：不能捕获一个改变的变量
```java
public void static repeat(String text, int count){
     for(int i = 0; i <= count; i++){
          ActionListener aListener = event ->{
            System.out.println(i);//Error，不能引用一个改变的变量
        }
     }
}
```

## 使用this
在一个 lambda 表达式中使用 this 关键字时, 是指创建这个 lambda 表达式的方法的 this参数
```java
public class Application(){
    public void init(){
        ACtionListener aLinstener = event -> {
            System.out.println(this.toString);
        }
    }
}
```
如上代码，调用的toString 会是Application的toString方法

# 处理Lambda表达式

使用 lambda 表达式的重点是延迟执行,如果想耍立即执行代码 , 完全可以直接执行 , 而无需把它包装在一个lambda 表达式中  

需要延迟执行的情况
1. 在一个单独的线程中运行代码
2. 多次运行代码
3. 在算法的适当位置运行代码 (例如,排序中的比较操作）
4. 发生某种情况时执行代码 (如,点击了一个按钮,数据到达,等等;）
5. 只在必要时才运行代码 。

例：重复n遍动作
```java
// Runnable是一个JAVA Api中提供的接口
public static void repeat(int n, Runnable action){
    for(int i = 0; i < n; i++) action.run();
}
//当执行action.run()时，就会执行lambda主体
repeat(10, () -> System.out.println("Hellp world"));
```

![基本数据类型的函数接口](https://raw.githubusercontent.com/FameLsy/Images/master/javase/基本数据类型的函数接口.png)

![常用函数接口](https://raw.githubusercontent.com/FameLsy/Images/master/javase/基本数据类型的函数接口.png)

# 设计函数接口

接口设计：
1. 设计一个接口，其中只有一个抽象方法
2. 可以用 @ FunctionalInterface 注解来标记这个接口

使用标记的好处
1. 如果不小心添加了一个抽象方法，编译器会产生错误消息
2. javadoc会指出该接口是函数接口