---
title: java 基础
tags: 
- java
categories:
- java
---

# 数据类型

|数据类型|存储需求|取值范围|前/后缀|
|--|--|--|--|
|int| 4字节|-2^32~ 2^32-1||
|short|2字节|-2^16~ 2^16 -1||
|long|8字节|-2^64~2^64 -1|后缀：L或l,如40000L|
|byte|1字节|-2^8 ~ 2^8 -1||
|float|4字节||后缀F或f|
|double|8字节||默认，后缀可以是D或d|
|char|1个字符|\u0000~\Uffff||
|boolean||true/false||

**JAVA范围与j运行ava代码机器无关**  
因为Java程序必须保证在所有机器上都能够得到相同的结果，所以各种数据类型的取值范围必须固定。

## 整型

|数据类型|存储需求|取值范围|前/后缀|
|--|--|--|--|
|int| 4字节|-2^32~ 2^32-1||
|short|2字节|-2^16~ 2^16 -1||
|long|8字节|-2^64~2^64 -1|后缀：L或l,如40000L|
|byte|1字节|-2^8 ~ 2^8 -1||

>注：
> 十六进制数：前缀0x或0X  
> 八进制数值：前缀0，如010,因为容易混淆，建议不常用  
> 二进制：从Java7开始，加上前缀0B或0b。（可为数值加_,如1_000_000,便于阅读）

## 浮点类型

|数据类型|存储需求|取值范围|前/后缀|
|--|--|--|--|
|float|4字节||后缀F或f|
|double|8字节||默认，后缀可以是D或d|

**浮点数值三个特殊值**  

所有浮点数值计算都遵循 *IEEE 754*。

|特殊值|对应常量|
|--|--|
|正无穷大|Double.POSITIVE_INFINITY 、 Float.POSITIVE_INFINITY|
|负无穷大|Double.NEGATIVE_INFINITY、Float.NEGATIVE_INFINITY|
|NaN(不是一个数)|Double.NaN、Float.NaN|

>注：  
> 浮点类型不适用于无法接受舍入误差的金融计算中，如2.0 -1.1 会输出0.8999999999999999，而不是0.9  
> 原因：浮点数值采用二进制系统表示法（二进制无法精确表示分数1/10）  
> 如果不允许输入误差，需要使用 *BigDecimal*类

## char 类型

|数据类型|存储需求|取值范围|前/后缀|
|--|--|--|--|
|char|1个字符|\u0000~\Uffff||

用于表示Unicode编码的字符单位的字符类型。  
char类型的字面量需要使用单引号括起来，如'A',对应65  
char类型的值可以表示为十六进制值，范围\u0000~\Uffff  

**转义序列\u**  
可以出现在加引号的字符常量或字符之外（其他的转义序列不可以）,如
```java
public static void main(String\u005B\u005D args)
```
其中\u005B\u005D代表 *[* 和 *]*

**特殊字符的转义序列**  

|转义序列|名称|Unicode值|
|--|--|--|
|\b|退格|\u0008|
|\t|制表|\u0009|
|\n|换行|\u000a|
|\r|回车|\u000d|
|\"|双引号|\u0022|
|\'|单引号|\u0027|
|\\|反斜杠|\u005c|

>警告：  
>Unicode转义序列会在解析代码之前先进行处理  
>如"\u0022 + \u0022", \u0022会在解析前转换成",最后结果为""+"",也就是一个空串  
>在注释中当心\u,如 // look some c:\users, 会出现语法错误（\u后面不是4个十六进制数）

# 变量
**变量命名**  
字母[a~zA~Z_$]
>注：  
> $ 虽然合法，但一般不使用在自己的代码中，只用于Java编译器或其他工具生成的名字中
> 命名：对于讲变量名命名为类型名，许多程序员喜欢加上前缀"a",如Box abox
## 常量
利用关键字final指示常量，只能赋值一次,且常量名习惯上使用全大写加 *_*的方式命名  
**类常量**  
通过关键字 *static final* 设置的常量

# 运算符

**算数运算**  
+、-、*、/、%（求余）

**数学函数**  

|函数|说明|
|--|--|
|Math.sin|sin|
|Math.cos||
|Math.tan||
|Math.atan(x)|返回一个 - π/2  到  π/2  弧度之间的数值|
|Math.atan2(y,x)|返回一个 -π 到 π 之间的数值，表示点 (x, y) 对应的偏移角度|
|Math.exp||
|Math.log||
|Math.log10||
|Math.PI|π|
|Math.E|e|
|Math.sqrt|算数平方根|
|Math.pow(x,a)|幂运算，计算x^a(double参数，返回double类型)|
|Math.round(x)|四舍五入,返回long类型|

**自增自减运算**
++、--

**关系运算符**  
*||*、*$$*

**三元运算符**
```java
condition ? expression1 : expression2;
```
**位运算**  
$、|、^、~、>>（左移,使用符号位填充高位）、<<（右移）、>>>(使用0填充高位)

## 数值类型转换
(虚线表示数据可能丢失)  
![数值类型转换](https://raw.githubusercontent.com/FameLsy/Images/master/javase/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20190106130122.png)

1. 如果两个操作数中有一个是 double 类型， 另一个操作数就会转换为 double 类型。
2. 否则， 如果其中一个操作数是 float 类型， 另一个操作数将会转换为 float 类型。
3. 否则， 如果其中一个操作数是 long 类型， 另一个操作数将会转换为 long 类型。
4. 否则， 两个操作数都将被转换为 int 类型

>注:  
> 如果对图中箭头反向转换，会报错，需要强制转换

# 枚举类
```java
Enum Size { SMALL, MEDIUM, LARGE, EXTRA .LARCE };
```

# 字符串
从概念讲，java字符串就是Unicode字符序列，每个用双引号括起来的字符串都是String类的一个实例。  
**Api**  
![string1](https://raw.githubusercontent.com/FameLsy/Images/master/javase/string.png)
![string2](https://raw.githubusercontent.com/FameLsy/Images/master/javase/string2.png)
![string3](https://raw.githubusercontent.com/FameLsy/Images/master/javase/string3.png)

**不可变字符串**  
Sting类没有提供用于修改字符串的方法，由于不能修改 Java 字符串中的字符， 所以在 Java 文档中将 String 类对象称为不可变字符串  
优点：编译器可以让字符串共享  

**equals和==**  
使用equals()而不使用==  
原因：  
==只能确定两个字符串地址是否相同，变量地址可能不同。(String重写了equals方法)
```java
public boolean equals(Object anObject) {
        if (this == anObject) {
            return true;
        }
        if (anObject instanceof String) {
            String anotherString = (String)anObject;
            int n = value.length;
            if (n == anotherString.value.length) {
                char v1[] = value;
                char v2[] = anotherString.value;
                int i = 0;
                while (n-- != 0) {
                    if (v1[i] != v2[i])
                        return false;
                    i++;
                }
                return true;
            }
        }
        return false;
    }
```

**空串与Null**  
空串是一个Java对象，有长度(0)和内容(空)  
null是特殊值，表示没有任何对象与该变量关联  
判断不为null和不为空串
```java
if (str != null && str.length() != 0)
```
**StringBuilder构建字符串**  
构建按键或来自文件中的单词。采用字符串连接的方式达到此目的效率比较低。每次连接字符串，都会构建一个新的String对象，既耗时，又浪费空间  
StringBuilder可以解决这个问题。
>注  
>StringBuilder类在JDK5.0加入，线程不安全  
>前身为StrgnBuffer,线程安全  

![StringBuilder](https://raw.githubusercontent.com/FameLsy/Images/master/javase/StringBuilder.png)

# 大数值
用于基本整型和浮点数精度无法满足需求。java.math提供了 *BigInteger*和 *bigDeciaml* 类，这两个类可以实现任意精度的浮点数运算。  
缺点是无法使用基本的算术运算符(+、-等)

# 数组
声明数组:
```java
 int[] a = new int[100];
 ```
数组初始化
```java
int[] smallPrimes = { 2, 3, 5, 7, 11, 13 };
```
初始化匿名数组
```java
new int[] { 17, 19, 23, 29, 31, 37 }
```
**数组工具类Array**  
![array1](https://raw.githubusercontent.com/FameLsy/Images/master/javase/Array1.png)

![array2](https://raw.githubusercontent.com/FameLsy/Images/master/javase/Array2.png)

# 类
## 类之间的关系

|关系|描述|
|--|--|
|依赖|uses-a|
|聚合|has-a|
|继承|is-a|

**UML符号**
![UML](https://raw.githubusercontent.com/FameLsy/Images/master/javase/UML.png)

##  Final域
构建对象时必须初始化这样的域。也就是说，必须确保在每一个构造器执行之后，这个域的值被设置，并且在后面的操作中，不能够再对它进行修改

## 静态域和静态方法
静态域（类域）：属于类，而不属于任何独立的对象，所有对象共享。  
静态方法：一种不能向对象实施操作的方法，没有隐式参数  
静态常量: static final修饰

## 使用静态方法的情况

1. 方法不需要访问对象状态，其所需参数都是通过显式参数提供（例如：Math.pow（））
2. 一个方法只需要访问类的静态域

## 工厂方法
通过静态方法来构造对象。
```java
xxx.getXXXInstance()
```

# 方法参数
方法参数共有两种类型: 基本数据类型、对象引用


>注:  
> 方法得到的是所有参数值的一个拷贝，特别是，方法不能修改传递给它的任何参数变量的内容。
> 对于一个基本数据类型，直接拷贝值，无论如何修改，原对象的值是不会改编的
> 对于一个对象引用，可以改变一个对象参数的状态
>  对于一个对象引用,不能让对象参数引用一个新的对象。（无法改变参数的引用）

# 对象构造
## 重载
多个方法有相同的名字、不同的参数，与返回类型无关  
## 默认初始化域
|类型|值|
|--|--|
|基本数值类型|0|
|布尔值|false|
|对象引用|null|
## 无参构造器
没有编写构造器，系统就会提供一个无参数构造器，并将值默认初始化

>注：  
> 参数名设置,如要在构造器显示传入一个name的参数，可以这样命名:String aName

## 调用另一个构造器
使用 this 关键字调用其他构造器,this放在第一行  
优点：对公共的构造器代码部分只编写一次

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
## 构造器调用处理步骤
1. 父类静态代码块
2. 子类静态代码块
3. 父类构造代码块
4. 父类构造函数
5. 子类构造代码块
6. 子类构造函数


# 作用域

|作用域|当前类|同一package|子孙类|其他package|
|--|--|--|--|--|
|public|√|√|√|√|
|protected|√|√|√|×|
|default|√|√|×|×|
|private|√|×|×|×|

# 继承
