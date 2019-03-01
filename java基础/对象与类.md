---
title: java 基础:对象与类
date: 2018-02-02 00:00:06
tags: 
- 对象与类
categories:
- JavaSe教程
---

# 类基础

## 名词概念
类：　对象的模板(template)  
对象：类的一个实例(instance)  
实例变量(instance variables)：定义在类中的数据或变量  
类的成员: 定义在类中的方法和实例变量

## this 关键字
this可以在引用当前对象的所有方法内使用

## 垃圾回收
使用new运算符来为对象动态地分配内存，这些对象是如何撤消的以及他们的内存在以后的重新分配时是如何被释放的？  

答：Java使用一种不同的、自动地处理重新分配内存的办法:垃圾回收( garbage collection)技术

垃圾回收机制工作原理：
当一个对象的引用不存在时,则该对象被认为是不再需要的,它所占用的内存就被释放掉。

## finalize( )方法
例如,如果一个对象正在处理的是非Java资源,如文件流,这时你要确认在一个对象被撤消以前要保证这些资源被释放。  

解决：Java提供了被称为收尾(finalization)的机制，使用该机制你可以定义一些特殊的操作,这些操作在一个对象将要被垃圾回收程序释放时执行。

为类添加收尾：只要定义finalize ( )方法

finalize()方法的通用格式:
```java
protected void finalize( )
{
// finalization code here
}
```


# 类进阶


## 参数数量可变的方法
如
```java
public  static double max(int aint, double... values)
```
该程序相当于传入了一个aint参数，和一个double[] 数组。

## 方法重载
方法重载（overloaded）：同一个类中的2个或2个以上的方法可以有同一个名字,只要它们的参数声明不同即可

在一些情况下,Java的自动类型转换也适用于重载方法的自变量  

如:当只有double类型的方法时，会把int扩大到double
```java
void test(double a) {
System.out.println("Inside test(double) a: " + a);
}

test(10);// print 10;
```

## 默认初始化域
|类型|值|
|--|--|
|基本数值类型|0|
|布尔值|false|
|对象引用|null|

## 构造器
### 无参构造器
没有编写构造器，系统就会提供一个无参数构造器，并将值默认初始化

>注：  
> 参数名设置,如要在构造器显示传入一个name的参数，可以这样命名:String aName

### 调用另一个构造器
使用 this 关键字调用其他构造器,this放在第一行  
优点：对公共的构造器代码部分只编写一次


### 构造器调用处理步骤

1. 父类静态代码块
2. 子类静态代码块
3. 父类构造代码块
4. 父类构造函数
5. 子类构造代码块
6. 子类构造函数

## 初始化块
在一个类的声明中， 可以包含多个代码块；只要构造类的对象，这些块就会被执行  
首先运行初始化块，然后才运行构造器的主体部分  
（不必需，不常见） 

## 静态初始化块
对类的静态域进行初始化的代码比较复杂，那么可以使用静态的初始化块
```
static{
    some code
}
```

## 参数传递

计算机语言给子程序传递参数的方法有两种:

1. 值传递：当一个简单类型传递给一个方法时,使用按值传递。将一个参数值(value)复制成为子程序的正式参数
2. 引用传递：对象传递使用引用传递。参数的引用(而不是参数值)被传递给子程序参数


## 类之间的关系

|关系|描述|
|--|--|
|依赖|uses-a|
|聚合|has-a|
|继承|is-a|

**UML符号**

![UML](https://raw.githubusercontent.com/FameLsy/Images/master/javase/UML.png)


# 嵌套类和内部类

嵌套类(nested classes): 在另一个类中定义的类,嵌套类可以访问嵌套它的类的成员,包括private成员。但是,包围类不能访问嵌套类的成员。

嵌套类一般有2种类型,前面加static标识符的和不加static标识符的
1. static的嵌套类：只能通过对象来访问它外部类的成员
2. 非static的嵌套类：又称内部类

内部类：非static的嵌套类,可以访问它的外部类的所有变量和方法

为什么使用内部类?  
1. 内部类方法可以访问该类定义所在的作用域中的数据, 包括私有的数据
2. 内部类可以对同一个包中的其他类隐藏起来
3. 当想要定义一个回调函数且不想编写大量代码时,使用匿名内部类比较便捷

## 使用内部类访问对象状态
内部类既可以访问自身的数据域, 也可以访问创建它的外围类对象的数据域

内部类含有一个隐式的引用参数，指向外部类。而这个引用，实在内部类的构造器中设置（编译器会修改内部类的构造器,添加一个外围类的引用参数）
```java
// 假设内部类的隐身引用变量为ｏuter
public innerClass(OuterlCLass outer){
    this.outer = outer;
}
```
## 内部类的特殊语法规则
内部类中引用外围类
```java
// 如OuterlCLass.this.name就是引用外围类的name变量
OuterCLass.this

```

更明确地编写内部类构造器
```java
// 如，this.new Node();编写一个内部Node类的构造器
outerObject.new InnerCLass(...);
```

外围类中引用内部类
```java
OuterClass.InnerClass;
```
## 局部内部类
局部内部类： 在方法中定义的类
1. 通常类只使用一次时可以将其定义为局部内部类
2. 局部类不能用 public 或 private 访问说明符进行声明.它的作用域被限定在声明这个局部类的块中
3. 局部类有一个优势, 即对外部世界可以完全地隐藏起来
```java
// 这里的InnerClass,只有start方法知道它的存在
public void start(){
    class InnerClass{
    public void method ( ActionEvent event){
        ...
    }
    InnerClass aInnerCLass = new InnerClass();
    ....
}
```
局部内部类访问不仅可以访问外部类，还可以访问局部变量,但那些局部变量必须事实上为 final。

## 匿名内部类
局部内部类: 只在某一个方法到一次
匿名内部类：只需要创建一次,无需为类命名

语法
```java
// 直接通过父接口来创建，方法实现直接写在{}
SuperType aClass = new SuperType(construction parameters){
    .....
}
```
## 静态内部类
只是为了把一个类隐藏在另外一个类的内部 , 并不需要内部类引用外围类对象,此时可以将内部类声明为static

> 注：  
>1. 在内部类不需要访问外围类对象的时候, 应该使用静态内部类
>2. 与常规内部类不同 , 静态内部类可以有静态域和方法 
>3. 声明在接口中的内部类自动成为 static 和 public 类(即接口中的都是静态公有内部类)


# 抽象类

## 抽象类/方法
关键字：abstract  
抽象类可以包含具抽象方法、具体数据、具体方法
```java
public abstract Clss person{
    private String name;
    public Person(String name){
        this.name = name;
    }
    public abstract String geDescrption();
}
```
类即使不含抽象方法，也可以将类声明为抽象类。  

抽象类不能被实例化。  

抽象类是被继承的。  

可以定义一个抽象类的对象变量，但只能引用非抽象子类的对象。

# 枚举类
如下枚举类Size，其中SMALL, MEDIUM, LARGE, EXTRA .LARCE是它的实例
```java
Enum Size { SMALL, MEDIUM, LARGE, EXTRA .LARCE };
```
1. 因为直接存在实例，所以比较两个枚举类时，直接使用==（不需要构造心对象）

如果需要，可以添加构造器，方法，域
```java
//SMALL("s"),, MEDIUM("x")就相当于使用构造器进行了实例化
public enum  Size {
    SMALL("s"), MEDIUM("x");
    private String name;
    private Size(String name){
        this.name = name;
    }
    public String getName(){return name;}
}
```
调用实例
```java
Size.SMALL.toString()
```
逆方法，指向枚举实例
```java
//可以通过字符串数据来获取实例
Size f = Enum.valueOf(Size.class, "SMALL");
//实际上与下面方法效果一样，但无法使用字符串来获取相应的实例
Size e = Size.SMALL;
```
获取枚举类的所有实例,返回一个数组
```java
Size[]values = Size.values();
```

![Enum](https://raw.githubusercontent.com/FameLsy/Images/master/javase/Enum.png)