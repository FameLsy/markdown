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

|作用域|当前类|同一package|子孙类|其他package|描述|
|--|--|--|--|--|--|
|public|√|√|√|√|对所有类可见|
|protected|√|√|√|×|对本包和所有子类可见|
|default|√|√|×|×|对本包可见,默认|
|private|√|×|×|×|仅对本类可见|

# 继承
关键字 extends 表示继承

## 覆盖方法

子类方法不能够直接地访问超类的私有域。需要通过super调用父类的getter方法。  
super 不是一个对象的引用， 不能将 super 赋给另一个对象变量， 它只是一个指示编 译器调用超类方法的特殊关键字。  
子类继承了父类的所有属性和方法或子类拥有父类的所有属性和方法，只不过父类的私有属性和方法，子类是无法直接访问到的。即只是拥有，但是无法使用

方法重写应遵循“三同一小一大”原则：  
>“三同”：即方法名相同，形参列表相同，返回值类型相同；  
>“一小”：子类方法声明抛出的异常比父类方法声明抛出的异常更小或者相等；  
>“一大”:子类方法的访问修饰符应比父类方法更大或相等。  

## 子类构造器
因为子类不能直接访问父类的私有域，所以必须利用父类的构造器对部分私有域进行初始化。  
通过 super 实现对超类构造器的调用。  
super 调用构造器的语句必须是子类构造器的第一条语句。  
子类的构造器没有显式地调用超类的构造器， 则将自动地调用超类默认（没有参数 ) 的构造器。  
如果超类没有不带参数的构造器， 并且在子类的构造器中又没有显式地调用超类 的其他构造器’ 则 Java 编译器将报告错误  

## 多态
一个对象可以指示多种实际类型的现象被成为多态。  
由父类引用子类对象，无法调用子类的特有方法。  
子类引用父类对象，需要强转。  

## 动态绑定
在运行时能够自动地选择调用哪个方法的现象被称为动态绑定。

## 理解方法调用

调用过程的详细描述：

1. 编译器査看对象的声明类型和方法名。【编译器已获得所有可能被调用的候选方法，同名不同参数的方法】  
2. 接下来，编译器将査看调用方法时提供的参数类型【编译器已获得需要调用的方法名字和参数类型。】。编译器没有找到与参数类型匹配的方法， 或者发现经过类型转换后 有多个方法与之匹配， 就会报告一个错误。
3. 如果是 private 方法、 static 方法、 final 方法或者构造器， 那么编译器将可以准确地知道应该调用哪个方法， 我们将这种调用方式称为静态绑定（ static binding )。
4. 当程序运行，并且采用动态绑定调用方法时， 虚拟机一定调用与 x 所引用对象的实 际类型最合适的那个类的方法。。假设 x 的实际类型是 D，它是 C 类的子类。如果 D 类定义了 方法 f(String，) 就直接调用它；否则， 将在 D 类的超类中寻找 f(String，) 以此类推。每次调用方法都要进行搜索，时间开销相当大。因此，虚拟机预先为每个类创建了一个 方法表（ method table), 其中列出了所有方法的签名和实际调用的方法。这样一来，在真正 调用方法的时候， 虚拟机仅查找这个表就行了。

## 阻止继承
final类:不允许扩展的类被称为 final 类(无法继承)。  
final方法：子类无法覆盖  

## 强制类型转换
设计习惯：  
在进行类型转换之前，先查看一下是否能够成功地转换。使用 instanceof 操作符就可以实现。  
```java
if (x instanceof C){
    demo = (C)x;
}
```
如果x不属于C，则返回false

（尽量少用类型转化和instanceof）

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

# Object
Object 类是 Java 中所有类的始祖， 在 Java 中每个类都是由它扩展而来的。
```java
package java.lang;

public class Object {

    private static native void registerNatives();
    static {
        registerNatives();
    }

    public final native Class<?> getClass();

    public native int hashCode();

    public boolean equals(Object obj) {
        return (this == obj);
    }


    protected native Object clone() throws CloneNotSupportedException;


    public String toString() {
        return getClass().getName() + "@" + Integer.toHexString(hashCode());
    }

    public final native void notify();

    public final native void notifyAll();

    public final native void wait(long timeout) throws InterruptedException;

    public final void wait(long timeout, int nanos) throws InterruptedException {
        if (timeout < 0) {
            throw new IllegalArgumentException("timeout value is negative");
        }

        if (nanos < 0 || nanos > 999999) {
            throw new IllegalArgumentException(
                                "nanosecond timeout value out of range");
        }

        if (nanos > 0) {
            timeout++;
        }

        wait(timeout);
    }

    public final void wait() throws InterruptedException {
        wait(0);
    }
    protected void finalize() throws Throwable { }
}
```

在 Java 中，只有基本类型 （ primitive types) 不是对象， 例如，数值、 字符和布尔类型的 值都不是对象。

## equals 方法
Object 类的equals方法判断两个对象是否具有相同的引用。  

**Java语言规范下的equals**
1. 自反性：对于任何非空引用x，x.equals(x)应该返回true
2. 对称性：对于任何引用x和y，当且仅当y.equals(x)返回true，x.equals(y)也应该返回true
3. 传递性：对于任何引用x、y、z，如果x.equals(y)返回true，y.equals(z)返回true，x.equals(z)也应该返回true
4. 一致性：如果x和y引用的对象没有发生变化，反复调用x.equals(y)应该返回同样的结果
5. 对于任何非空引用x，x.equals(null)应该返回true

**完美equals建议**
```java
public class E{
    public boolean equals(Object otherObject){
        //先判断地址相同
        if(this == otherObject) return true;
        //如果传入参数为null，无需比较
        if(otherObject == null) return false;
        //类不同，肯定不同
        if(getClass() != otherObject.getClass()) return false
        //此时可知参数是非null 的E类了
        E other = (E)otherObject;
        //比较直，对象用equals比较，防止null值;数值用==比较
        return Objects.equals(name, other.name)
        && phone == other.phone
    }
}
```

## hashCode方法

散列码（ hash code ) 是由对象导出的一个整型值，其值为对象的 *存储地址*  
如果重新定义 equals方法，就必须重新定义 hashCode 方法， 以便用户可以将对象插入到散列表中  
如：这是一个由name、salary、hireDay组成的hashCode编码。
```java
public int hashCode(){
    return Objects.hash(name, salary, hireDay)
}
```

![hashcode](https://raw.githubusercontent.com/FameLsy/Images/master/javase/hashcode.png)

## toString方法
强烈建议为自定义的每一个类增加toString方法。

# 泛型
指定保存元素的对象类型，使用一对尖括号。  
如 *ArrayList*是一个泛型类
```java
ArrayList<C> staff = new ArrayList<C>();
```
java7.0后可以省略右边的类型参数
```java
ArrayList<C> staff = new ArrayList<>();
```
## 类型化与原始数据的兼容性
以ArrayList为例,
有如下方法,属于没有使用到类型化的设置
```java
public void update (ArrayList list){}
public ArrayList find(String query){}
```
将类型化的ArrayList传递给update作为参数,是不会有任何警告的(尽管并不安全)
```java
ArrayList<C> staff = new ArrayList<>();
update(staff)
```
但如果是将一个原始的ArrayList赋给一个类型化的ArrayList,会出现警告
```java
ArrayList(E) result = find(query);//yields warning
```
即使强转，也会出现另一个警告
```java
ArrayList(E) result = (ArrayList(E))find(query);//yields warning
```
解释：  
其实编译器在对类型进行转换时，没发现违规现象，会将所有类型化数据转换成原始数据，也就是说在虚拟机中没有类型参数。只要确保不会造成严重的后果即可.  
可以使用@SuppressWamings("unchecked") 标注来标记 这个变量能够接受类型转换
```java
@SuperessWarnings("unchecked")  ArrayList(E) result = (ArrayList(E))find(query);//yields warning
```

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

# 参数数量可变的方法
如
```java
public  static double max(int aint, double... values)
```
该程序相当于传入了一个aint参数，和一个double[] 数组。

# 反射
大量应用于JavaBeans中，是Java组件的体系结构。  
反射可以用来:
1. 在运行时分析类的能力
2. 在运行时查看对象，如编写一个toSring方法供所有类使用
3. 实现通用的数组操作代码
4. 利用Method对象

## Class 类
Class类：Java运行时系统始终为所有的对象维护一个被成为运行时的类型标识。这个信息跟踪每个对象所属的类，保存这些信息的类被称为Class。

获取Class对象：

```java
//方法1：静态方法(应该提供一个异常处理器)
Class.forName(className);
//方法2：Object的getClass()方法
e.getClass();
//方法3：
Class cl1 = int.class;//int不是类，但int.class是一个类
Class cl2 = Double[].class
```

返回类名：
```java
Object.getClass().getName();
```

比较：  
虚拟机为每个类型管理一个Class对象。所以class比较只需要用==即可

创建实例：

```java
e.getClass().newInstance();
//forName()配合使用
String s = "java.util.Random";
Object m = Class.forName(s).newInstance();
```

 ![反射](https://raw.githubusercontent.com/FameLsy/Images/master/javase/反射.png)

 ## 利用反射分析类的能力

**检查类的结构**  
java.lang.reflect中的三各类

|类|描述|
|--|--|
|Field|类的域|
|Method|类的方法|
|Constructor|类的构造器|

如下图  
getFields、getMethods、getConstructors返回类提供的public的域、方法和构造器数组。  
getDeclaredFields、getDeclaredMethods、getDeclaredConstructors返回类中所有的域、方法和构造器数组
![Class](https://raw.githubusercontent.com/FameLsy/Images/master/javase/Class.png)

如下图，出特别标注外，其他都是Field、Method、Constructor的公有方法
![反射2](https://raw.githubusercontent.com/FameLsy/Images/master/javase/反射2.png)

Modifier类可以通过返回的整数数值，进行相关判断。

![Modifier](https://raw.githubusercontent.com/FameLsy/Images/master/javase/Modifier.png)

## 在运行时使用反射分析对象


# 继承的设计技巧

1. 将公共操作和域放在超类
2. 不要使用受保护的域
3. 使用继承实现“ is-a” 关系
4. 除非所有继承的方法都有意义，否则不要使用继承
5. 在覆盖方法时，不要改变预期的行为
6. 使用多态， 而非类型信息
```java
//如下情况，考虑多态，即将方法写入两个类的父类或接口中
if (x is of type1){
    action1(x)
}else if(x is of type2){
    action2(x)
}
7. 不要过多地使用反射