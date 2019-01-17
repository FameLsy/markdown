---
title: java基础5：lambda表达式
tgas: 
- java基础
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

## 函数式接口

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

## 方法引用
使用现成的方法传递到其他代码的某个动=动作
如
```java
Timer t = new Timer(1000, event -> System.out.pringtln(event));
```