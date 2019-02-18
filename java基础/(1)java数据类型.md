---
title: java 基础(1):数据类型
date: 2018-02-02 00:00:01
tags: 
- 数据类型
categories:
- JavaSe教程
---

Java语言中3个最基本的元素:数据类型,变量和数组

java一共有8个基本数据类型

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

# 整数类型

|数据类型|存储需求|取值范围|前/后缀|
|--|--|--|--|
|long|8字节|-2^64~2^64 -1|后缀：L或l,如40000L|
|int| 4字节|-2^32~ 2^32-1||
|short|2字节|-2^16~ 2^16 -1||
|byte|1字节|-2^8 ~ 2^8 -1||

## 字节型(byte)
最小的整数类型是字节型。它是有符号的8位类型,数的范围是-128~127。当你从网
络或文件处理数据流的时候,字节类型的变量特别有用。

## 短整型(short)
有符号的16位类型,被定义为高字节优先(称为big-endian格式),它可能是Java中使用得最少的类型。主要适用于16位计算机。

## 整型(int)
有符号的32位类型。int类型的变量通常被用来控制循环及作数组的下标。任何时候你的整数表达式包含byte,short,int及字面量数字,在进行计算以前,所有表达式的类型被提升(promoted)到整型。

使用字节型和短整型可以节约空间,但是不能保证 Java 不会内部把那些类型提升到整型；记住，类型决定行为,而不是大小(惟一的例外是数组,字节型的数据保证每个数组元素只占用一个字节,短整型使用 2 个字节,整型将使用4个。)

## 长整型(long)

有符号的64位类型,长整型数的范围是相当大的。这使得大的、整个数字都被需要时,它是非常有用的

## 浮点类型
当计算的表达式有精度要求时被使用 ,所有浮点数值计算都遵循 *IEEE 754*。

|数据类型|存储需求|取值范围|前/后缀|
|--|--|--|--|
|float|4字节||后缀F或f|
|double|8字节||默认，后缀可以是D或d|


## 单精度浮点型(float)
单精度浮点型(float)专指占用32位存储空间的单精度(single-precision)值。  
当值很大或很小的时候,它将变得不精确。当你需要小数部分并且对精度的要求不高时,单精度浮点型的变量是有用的。

## 双精度型(double)浮点型
占用64位的存储空间  
所有超出人类经
验的数学函数,如sin( ),cos( ),和sqrt( )均返回双精度的值  

## 浮点数值三个特殊值

|特殊值|对应常量|
|--|--|
|正无穷大|Double.POSITIVE_INFINITY 、 Float.POSITIVE_INFINITY|
|负无穷大|Double.NEGATIVE_INFINITY、Float.NEGATIVE_INFINITY|
|NaN(不是一个数)|Double.NaN、Float.NaN|

>注：  
> 浮点类型不适用于无法接受舍入误差的金融计算中，如2.0 -1.1 会输出0.8999999999999999，而不是0.9  
> 原因：浮点数值采用二进制系统表示法（二进制无法精确表示分数1/10）  
> 如果不允许输入误差，需要使用 *BigDecimal*类

# 字符类型(char)

|数据类型|存储需求|取值范围|前/后缀|
|--|--|--|--|
|char|1个字符|\u0000~\Uffff||

用于表示Unicode编码的字符单位的字符类型。  
char类型的字面量需要使用单引号括起来，如'A',对应65  
char类型的值可以表示为十六进制值，范围\u0000~\Uffff  

尽管char不是整数,但允许你可以将2个字符相加,或对一个字符变量值进行增量操作
```java
char ch1 = 'x';
ch1++; //ch1会变成y
```

# 布尔型（boolean）
表示逻辑值的简单类型，它的值只能是真或假这两个值中的一个

# 字面量
## 整数字面量
任何一个数字的值就是一个整数字面量,例如1,2,3和42  

十六进制数：前缀0x或0X   

八进制数值：前缀0，如010,因为容易混淆，建议不常用  

二进制：从Java7开始，加上前缀0B或0b。（可为数值加_,如1_000_000,便于阅读）  


## 浮点字面量
浮点数代表带小数部分的十进制值,可通过标准记数法或科学记数法来表示  

标准记数法(Standard notation)由整数部分加小数点加小数部分组成,2.0,3.14159,和0.6667  

科学记数法(Scientific notation)是浮点数加一表明乘以10的指定幂次的后缀,指数是紧跟E或 e 的一个十进制的数字,它可以是正值或是负值,例子如6.022E23,314159E-05,及2e+100默认为double类型。

## 布尔型字面量
布尔型字面量仅仅有2个逻辑值,真或假（true or false）  
Java中,真字面量的值不等于1,假字面量的值也不等于0  

## 字符字面量
Java用Unicode字符集来表示字符,Java的字符是16位值,可以被转换为整数并可进行
像加或减这样的整数运算。  

所有可见的ASCII字符都能直接被包括在单引号之内,例如'a','z',and '@'。  

不能直接被包括的字符,有若干转义序列，例如'\n'代表换行符字符。

为直接得到八进制或十六进制字符的值也有一个机制：
1. 对八进制来说,使用反斜线加3个阿拉伯数字。例如, '\141'是字母'a'。
2. 对十六进制来说,使用反斜线和u (\u)加4个十六进制阿拉伯数字。例如,'\u0061'因为高位字节是零,表ISO-Latin-1字符集中的'a'。

**转义序列\u**  

可以出现在加引号的字符常量或字符之外（其他的转义序列不可以）,如
```java
public static void main(String\u005B\u005D args)
```
其中\u005B\u005D代表 *[* 和 *]*

**特殊字符的转义序列**

|转义序列|名称|Unicode值|
|--|--|--|
|\ddd|八进制字符(ddd)|
|\uxxxx|十六进制Unicode码字符|
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

## 字符串字面量
将一系列字符用双引号括起来,例如"Hello World"

# 数值类型转换
分为自动类型转换和强制类型转换。

## 自动类型转换

满足以下条件将自动转换
1. 这2种类型是兼容的。
2. 目的类型数的范围比来源类型的大。

当以上2个条件都满足时,拓宽转换(widening conversion)发生  

数字类型和字符类型(char)或布尔类型(bollean)是不兼容的。字符类型(char)和布尔类型(bollean)也是互相不兼容的。

## 强制转换

通用格式
```java
(target-type)value
```

截断(truncation):把浮点值赋给整数类型时一种不同的类型转换,小数部分会被舍去。

java 257强制转换赋值给byte会怎么样？323呢?  
答：byte范围是256,所以257赋值后，值为1(257/256)。323为67(323/256)

## 基本数值类型直接的转换
(虚线表示数据可能丢失)  
![数值类型转换](https://raw.githubusercontent.com/FameLsy/Images/master/javase/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20190106130122.png)


>注:  
> 如果对图中箭头反向转换，会报错，需要强制转换


# 表达式中的类型提升
如下代码所示：
```java
byte a = 50;
byte b = 20;
int i = b * a; //ok
b = b * 2;//error,cannot assign an int to a byte
b = (byte)b; //ok
```

所有byte型和short型的值在表达式中会被提升到int类型  
1. 好处：避免了计算过程中中间结果超出范围
2. 缺点，需要强转

类型提升规则：
1. 如果两个操作数中有一个是 double 类型， 另一个操作数就会转换为 double 类型。
2. 否则， 如果其中一个操作数是 float 类型， 另一个操作数将会转换为 float 类型。
3. 否则， 如果其中一个操作数是 long 类型， 另一个操作数将会转换为 long 类型。
4. 否则， 两个操作数都将被转换为 int 类型

































# 4 枚举类
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



# 5 字符串
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
null == null 返回true
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

# 6 大数值
用于基本整型和浮点数精度无法满足需求。java.math提供了 *BigInteger*和 *bigDeciaml* 类，这两个类可以实现任意精度的浮点数运算。  
缺点是无法使用基本的算术运算符(+、-等)


# 对象包装器和自动装箱
基本类型对应的类，被成为包装器。


|包装器|原始|
|--|--|
|Integer|int|
|Long|long|
|Float|float|
|Double|double|
|Short|short|
|Byte|byte|
|Character|char|
|Void|void|
|Boolean|boolean|

前六个包装器继承于Number类。  
使用：  
```java
ArrayList<Integer>//不能携程ArrayList<int>
```
>注：  
>装箱和拆箱是编译器认可的，而不是虚拟机（编译器在生成类的字节码的时候，插入必要的方法调用，而虚拟机这是执行这些字节码）

## 自动装箱
将基本数据赋值给包装器
```java
list.add(3);
//自动装箱成
list.add(Integer.valueOf(3));
```
 ## 自动拆箱
 将包装器对象赋值给基本数据
 ```java
 int n = list.get(i);
 //自动拆箱
 int n = list.get(i).intValue();
 ```

 ![Integer](https://raw.githubusercontent.com/FameLsy/Images/master/javase/Integer.png)

## 混用Integer和Double
Integer会先自动拆箱，提升为Double，在装箱成Double
```java
Integer n = 1;
Double x = 2.0;
System.out.println(true ? n : x);
```