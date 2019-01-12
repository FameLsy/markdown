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

查看数据域的实际内容：  
其中，Object obj 是一个包含该域的对象。
```java
Field.get(Object obj)
```
实例:  
```java
Employee maorl = new Employee("maorl", 39999, 10, 1, 1994);
Class aClass = maorl.getClass();
// name域是Employee的一个私有域
Field aField = aClass.getDeclareField("name");
//获取具体值
Object value = aField.get(masorl);
```
在这里，name是一个私有域，因为反射机制的默认是受Java访问控制的，所以get()方法会抛出一个IllegalAccessException。  
如果一个Java程序没有受到安全管理器的控制，就可以覆盖访问控制。为了达到这个目的，可以使用Field、Method、Constructor对象的 *setAccessible*
```java
aFiled.setAcessible(true)
```
此时，我们可以访问得到name域的值了。  

**get()自动打包**  
对于get()方法如果域是一个String,那么返回成Object是没有问题的。但如果是一个基本数据类型，get()方法会自动将值打包成对象。

**设置域**  
get()方法用来获取域的值，那么对应的，set(Obj, value)方法用来设置域值
```java
aFiled.set(maosrl, "newName");
```

![反射3](https://raw.githubusercontent.com/FameLsy/Images/master/javase/反射3.png)

## 使用反射编写泛型数组代码

如何自己编写一个通用数组拷贝的的方法呢？
```java
 /**
     * 数组拷贝
     * 主要为题：如要拷贝一个Employee数组，在执行玩方法后返回的是Object，需要能够成功强转
     *
     * 1. 解决的关键为Array.wInstance(Class<?> componentType, int length),它可以创建一个新的指定数组元素类型的数组
     * 2. 获取数组元素类型：Class.getComponentType(),该方法可以获取到数组类的元素类型
     * 3. 复制数组：System.arraycopy(Object src, int srcPos, Object dest, int destPos,int length)
     *      Object src：需要复制的源数组
     *      int srcPos：源数组复制起始位置
     *      Object dest：复制的目标数组
     *      int destPos：复制的目标数组的起始位置
     *      int length: 复制长度
     *
     * @param obj
     * @param newLength
     * @return
     */
    public static Object goodCopyOf(Object obj, int newLength){
        Class<?> aClass = obj.getClass();
        if (!aClass.isArray()) return null;
        Class<?> componentType = aClass.getComponentType();
        int length = Array.getLength(aClass);
        Object newArray = Array.newInstance(componentType, newLength);
        System.arraycopy(obj, 0, newArray, 0, Math.min(newLength, length));
        return newArray;
    }
```

以下是Arrays.copyOf()的方法  
> 1. copyOf()两个参数的方法调用了三个参数的方法，把原始的数组类传了进去。  
> 2. 通过三元运算符判断原始数据是否属于Object[]数组  
>       是Object就直接new Object[]  
>       否则需要调用Array.newInstance方法来创建新数组
```java
    public static <T> T[] copyOf(T[] original, int newLength) {
        return (T[]) copyOf(original, newLength, original.getClass());
    }
    
    public static <T,U> T[] copyOf(U[] original, int newLength, Class<? extends T[]> newType) {
        @SuppressWarnings("unchecked")
        T[] copy = ((Object)newType == (Object)Object[].class)
            ? (T[]) new Object[newLength]
            : (T[]) Array.newInstance(newType.getComponentType(), newLength);
        System.arraycopy(original, 0, copy, 0,
                         Math.min(original.length, newLength));
        return copy;
    }
```

![Array3](https://raw.githubusercontent.com/FameLsy/Images/master/javase/Array3.png)

## 调用任意方法
Java是没有方法指针的，即将一个方法的存储地址传给另一个方法，以便第二个方法能够调用它。

通过 Class.pgetMethod(String name, Class<?>... parameterTypes)来获取Method，需要提供方法的参数类。

**调用方法**：  
1. 使用Method.invoke(Object obj, Object... args)进行调用  
 第一个obj为隐式参数(静态方法就传null,有点类似于feild.get()方法)  
 第二个数组对象为显示参数（在JavaSE 5.0以前必须传递，没有显示参数就传0）  

2. 因为其返回类型为Object，所以需要进行相应的类型转换  
3. 如果返回值是基本数据类型，会自动包装
```java
    public static void main(String[] args) throws Exception {
        Employee maosrl = new Employee("maosrl", 1000);
        Method toString = Employee.class.getMethod("toString");
        System.out.println((String)toString.invoke(maosrl));
    }
```

![Method](https://raw.githubusercontent.com/FameLsy/Images/master/javase/Method.png)
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
```

# 接口

接口方法都是public,可以不写。但是实现接口的时候必须写（实现类不写是default）

## 接口特性

1. 无法实例化
2. 能声明接口的变量
3. 可以使用 instanceof 检查类是否属于某个特定的接口
4. 接口可以继承接口
5. 接口不能包含实例域和静态方法(java 8.0后可以添加静态方法)
6. 接口可以包含常量
7. 接口方法自动设置为public abstract,接口域自动设置为public static final

## 接口与抽象类

为什么要引入接口？  
接口可以多实现，而抽象类只能单继承

## 默认方法

可以为接口方法提供一个默认方法,使用default 修饰符标记
```java
public interface Comparable<T>{
    default int compareTo(T other){return 0;}
}
```
一般情况下没什么用(实现Comparable接口时会覆盖这个方法),但可以在如下环境使用：

这样，把所有方法都设置为默认，程序员只需覆盖关心的方法即可
```java
public interface Demo(){
    default void method1(){}
    default void method2(){}
    default void method3(){}
    default void method4(){}
    default void method5(){}
}
```

默认方法的另一个重要用途： *接口演化*  

假设我们现在有一个接口 A, Mas是其的一个实现类
```java
public interface A{...}
publiic Class Max implements A{...}
```
后来，我们为A添加了一个新方法 newMethod
```java
public interface A{
    void newMethod(){}
}
```
可能会出现以下情况  
1. Mas类编译将会失败（没有重写newMethod）
2. 不重新编译Mas类，在Mas调用newMethod时抛出AbstractMethodError

如果变为defalut方法，以上两个问题都会解决  
1. Mas成功编译
2. 不重新编译Mas,调用时会向上调用A接口的newMethod

## 解决默认方法冲突
　
接口与父类冲突：父类优先  
如接口定义了默认方法getName(),而父类中也定义了getName()方法，默认方法被忽略  

接口与接口冲突： 手动覆盖  
如接口A、Ｂ同时定义了getName()方法，必需覆盖解决冲突。  
也可以在类中选择二者其一
```java
Class Choose implements A,B{
    public String getName(){
        return A.super.getName();
    }
}　
```

另一种情况：如果接口Ａ、Ｂ的方法是非默认方法，无论是否另一个有同名默认方法，必需重写

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

# 内部类（inner class）

为什么使用内部类?  
1. 内部类方法可以访问该类定义所在的作用域中的数据, 包括私有的数据
2. 内部类可以对同一个包中的其他类隐藏起来
3. 当想要定义一个回调函数且不想编写大量代码时,使用匿名内部类比较便捷


# 异常

## 异常分类

异常对象都是派生于 Throwable 类的一个实例,但在下一层立即分解为两个分
支 : Error 和 Exception

**Error**  
Java 运行时系统的内部错误和资源耗尽错误

**Exception**  
真正需要关心的异常，分解为两个分支 :
1. RuntimeException :程序错误导致的异常属于 RuntimeException
2. 其他异常 :程序本身没有问题,但由于像I/O错误这类问题导致的异常属于其他异常（ＩＯException）

**RuntimeException包含情况**  
1. 错误的类型转换
2. 数组访问越界
3. 访问dnull指针

**其他异常包含情况**  
1. 试图在文件尾部后面读取数据
2. 试图打开一个不存在的文件
3. 试图根据给定的字符串查找 Class 对象, 而这个字符串表示的类并不存在

**受查异常和非受查异常**  
非受查异常: 派生于Error类或RuntimeException类的所有异常称为非受查( unchecked)异常  
受查异常：其他的异常称为受查 ( checked ) 异常

## 声明受查异常
如果是受查异常，应该在方法首部声明所有可能抛出的异常(通过关键字throws)  
如下是Java类库中的一个类的构造器方法的声明
```java
public FilelnputStream ( String name ) throws FileNotFoundException
```

什么时候应该声明抛出异常？
1. 调用一个抛出受査异常的方法
2. 程序运行过程中发现错误 , 并且利用 throw 语句抛出一个受查异常
3. 对于Java 的内部错误（Error），不必声明
4. RuntimeException,不必声明
5. 总结：方法必须抛出所有可能抛出的受查异常，非受查异常要么不可控制(Error)要么就应该避免发生(RuntimeException)

**子类异常和父类异常**  

1. 在子类中覆盖了父类的一个方法 ,子类方法中声明的受查异常不能比父类方法中声明的异常更通用(子类方法中可以抛出更特定的异常, 或者根本不抛出任何异常)
2. 父类类方法没有抛出任何受查异常, 子类也不能抛出任何受查异常

## 抛出异常
如抛出一个EOFException异常(IOException子类)
```java
throw EOFException();
```
别忘记在方法首部声明抛出的异常

## 自定义异常
继承Exception 或　Exception子类  
一般异常有两个构造函数：

1. 一个无参构造器
2. 一个记录详细描述信息(超类Throwable.toString可以打印这些信息)

如：
```java
public MyExcpetion extends IOexception{
    public MyExcpetion(){}
    public MyExcpetion(String message){
        super(message)
    }
}
```

## 捕获异常
使用try/catch 块
```java
try{
    more code
}
catch ( ExceptionType e )
{
    handler for this type
}
```

如果子类覆盖了父类的方法，但父类没有声明任何异常抛出，那么子类必须捕获每一个抛出的受查异常（父类方法没抛出异常，子类也不能抛出异常）

**捕获多个异常**  

```java
try{
    ...
}
catch ( FileNotFoundException e ){
    ...
}
catch ( UnknownHostException e ){
    ...
}
catch ( IOException e ){
    ...
}
```

**合并catch**  
如果两个或多个异常的处理动作相同的情况下使用
```java
try{
    ...
}
catch ( FileNotFoundException | nknownHostException e ){    
    ...
}
```
>注：  
>捕获多个异常时, 异常变量隐含为 final 变量。即不能为e赋值不同的值

## 再次抛出异常与异常链
在catch中抛出异常,目的是为了改变异常类型  
方式一：
```java
try{
access the database
}
catch ( SQLException e ){
    throw new ServletException (" database error: " + e.getMessage());
}
```
方式二：更好的处理，并将原始异常设置成新异常的原因（推荐）  
Throwable.initCause(Throwable cause) : 将Throwable的cause初始化为指定的值（不能是它本身）
Throwable.getCause():获取cause值
```java
try{
    ...
}
catch ( SQLException e ){
    Throwable se = new ServletException("database error");
    // 	Throwable.initCause(Throwable cause) 
    se.initCause(e);
    throw se;
}

// 获取原始异常Throwable.getCause() 
Throwabl e = se.getCasue();
```
## finally子句
可以这样使用,有异常抛出就直接执行finally中的语句
```java
try{

}finally{

}
```

**建议解耦try/catch和try/finally**  
如下：
```java
InputStrean in = ...;
try{
    try{
        ...
    }finally{
        in.closed();
    }
}catch(IOExcpetion o){
    ...
}
```
好处：  
1. 内层try/finally只有一个职责，确保关闭流
2. 外层try/catch也只有一个职责，报告异常
3. 这样设计不仅清楚，还能报告finally中的异常

**finally与return**    

finally 中包含return,会覆盖原始方法中的return值  
如,执行ｆ(2),将会返回０
```java
public static int f (int n ){
    try{
        int r = n * n;
        return r;
    }finally{
        if ( n == 2) return 0 ;
     }
}
```

**finally抛出异常带来的麻烦**  
如,假设在try中抛出了一些非IOException, 而finally中的closed()方法也有可能抛出异常，这时，原始异常丢失，抛出closed方法的异常
```java
 try{
        ...
}finally{
        in.closed();
}
```
解决也十分繁琐
```java
InputStream in = ...;
Exception ex = null ;
try{
    try{
        code that might throw exceptions
    }catch (Exception e){
        ex = e;
        throw e;
    }
}finally{
    try{
        in.closed();
    }catch(Exception e){
        if(ex == null) throw e;
    }
}
```

更好的解决办法:  
Java SE 7 新增的资源关闭处理.

## 带资源的try语句
try-with-resource最简形式：
```java
try (Resource res = ...；Ｒesource res2 = ...){
    ...
}
```
当try块退出时，自动执行res.closed()。  
对于colosed()可能抛出的异常，原来的异常抛出，而closed()异常会被自动捕获,并由Throwable.addSuppressed()添加到原来的异常中。可以使用ThrowablegetSuppressed()获取。

## 分析堆栈轨迹元素
堆栈轨迹 ( stack trace ) 是一个方法调用过程的列表 , 它包含了程序执行过程中方法调用的特定位置，在java正常终止，无捕获异常时显示该列表。

对于获取堆栈信息有以下几种方法：

1. Throwable.printStackTrace()方法
2. Throwable.getStackTrace()方法
3. 静态Tread.getAllStackTrace()方法（产生所有线程的堆栈轨迹）

如下是打印递归函数facorial的堆栈情况
```java
import java.util.Scanner;

public class StackTrace {
    /**
     * 使用了throwable.getStackTrace()来获取StackTraceElement数组
     * StackTraceElement 类含有能够获取文件名，和当前执行代码号的方法，同时还有获取类名和方法名的方法
     * 本例如果输入n=3时，输出结果如下;
     * ---------------------------
     * factorial(3):
     * StackTrace.facorial(StackTrace.java:14)
     * StackTrace.main(StackTrace.java:30)
     * factorial(2):
     * StackTrace.facorial(StackTrace.java:14)
     * StackTrace.facorial(StackTrace.java:21)
     * StackTrace.main(StackTrace.java:30)
     * factorial(1):
     * StackTrace.facorial(StackTrace.java:14)
     * StackTrace.facorial(StackTrace.java:21)
     * StackTrace.facorial(StackTrace.java:21)
     * StackTrace.main(StackTrace.java:30)
     * return1
     * return2
     * return6
     * -----------------------------
     * @param n
     * @return
     */
    public static int facorial(int n){
        System.out.println("factorial(" + n + "):");
        Throwable throwable = new Throwable();
        StackTraceElement[] stackTraces = throwable.getStackTrace();
        for (StackTraceElement stackTraceElement : stackTraces){
            System.out.println(stackTraceElement);
        }
        int result;
        if (n <= 1) result = 1;
        else result = n * facorial(n-1);
        System.out.println("return" + result);
        return result;
    }

    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        System.out.println("Enter n: ");
        int n = in.nextInt();
        facorial(n);
    }
}
```
![throwable1](https://raw.githubusercontent.com/FameLsy/Images/master/javase/throwable1.png)
![throwable2](https://raw.githubusercontent.com/FameLsy/Images/master/javase/throwable2.png)
![Exception](https://raw.githubusercontent.com/FameLsy/Images/master/javase/Exception.png)

## 使用异常机制技巧
1. 异常处理不能代替简单的测试
2. 不要过分地细化异常
3. 利用异常层次结构  
    不要只抛出 RuntimeException 异常 。 应该寻找更加适当的子类或创建自己的异常类 。  
    不要只捕获 Thowable 异常
4. 不要压制异常
5. 在检测错误时, “ 苛刻 ” 要比放任更好  
    在用无效的参数调用一个方法时 , 返回一个虚拟的数值, 还是抛出一个异常 , 哪种处理方式更好  
    如,当栈空时, Stack.pop() 是返回一个 null , 还是抛出一个异常?  
    在出错的地方抛出一个 EmptyStackException异常要比在后面抛出一g个NullPointerException 异常更好
6. 不要羞于传递异常

# 断言(可以使用Junit代替)


断言机制：在测试期间向代码插入一些检查语句，当代码发布时，检查语句会被移走  
两种断言的形式：

1. assert 条件;
2. assert 条件 : 表达式;

两种形式均会对条件进行检测，如果为false,抛出AssertionError异常。对于形式2，表达式会传入AssertionError构造器，并转换成一个消息字符串。

> 注：  
>表达式唯一目的就是产生一个消息字符串  
>AssertionError对象并不存储表达式的值

## 启用禁用断言
默认情况下，断言是被禁用的。  
>注:  
> 启用或禁用断言无需重新编译程序  
> 启用或禁用断言属于类加载器的功能  
> 断言禁用，类加载器直接跳过断言代码  


启用断言：
```java
java -enableassertions MyApp
//或
jvava -ea : MyApp
```
在某个类或整个包中使用断言:
```java
java -ea: MyClass -ea : com.mycompan.mylib
```

禁用断言
```java
java -disableassertions MyClass
//或
java -da : MyClass
```

需要应用到系统类的断言启用
```java
java -enablesystemassertions Myclass
//或
java -esa : Myclass
```

## 使用断言完成参数检查

java语言中的3种处理系统错误机制：
1. 抛出一个异常
2. 日志
3. 使用断言

断言使用情况：
1. 断言失败是致命的，不可恢复的错误
2. 断言检查只用于开发和检测阶段

# 记录日志（Log4j）

# 泛型程序

## 泛型类
类型变量使用大写形式，在Java中
1. E表示集合的元素类型
2. K和V表示关键字与值类型
3. T(需要时还可以使用临近字母U,S)表示任意类型
```java
public class MyCLass<T,U>{
    ...
}
```

## 泛型方法
类型变量放在修饰符后面，返回值前面
```java
    public static <T> T get(T... a){
        ...
    }
```
泛型方法可以定义在普通类中，也可以定义在泛型类中

注意以下的不同  
这是另一种包含类型变量的写法
```java
public T get(T... a(){

}
```
两种写法的区别在于
>对于<T> T,可以在调用方法时指定Ｔ的类型，如MyClass.<String> get()，获取的返回值就是String类  
> 而对于T,只能使用普通的MyClass.get(),至于返回值类型，会根据ＭyClass<T>类型变量决定。


　　
**调用泛型方法**  
在方法名前的尖括号中放入具体类
```java
MyClass.<String>get("a", "b", "c");
//或,返回值根据类型自动选择
MyClass.get("a", "b", "c");

```

**泛型方法可能出错**  
如下，编译器会自动打包一个Double类和两个Integer类，而后寻找他们的共同超类。  
事实上会找到Number和Comparable接口，其本身也是个泛型类。  
这样会导致解释代码有两种方法，且都合法。
```java
MyClass.method(3.14, 2, 1);
```

## 　类型变量限定

如下，Ｔ可以是任意类型
```java
public static <T> T min(T[] a){
    ...
}
```
但如果我们需要限制Ｔ必须实现Comparable接口
```java
public static <T extends Comparable> T min(T[] a){
    ...
}
```
>注：  
>可以看到，在上面例子中实现接口用的是extends而不是implements  
>原因：其实< T extends Compatable >应该是< T extends BoundingType >,即代表的含义是Ｔ是 BoundingType的子类型。而不是Ｔ继承或实现BoundingType。（Java设计者不打算添加新的关键字，而extends更接近子类的意思）

**多个限定**　　
使用 *&* 来分隔限定类型,使用 "," 来分隔类型变量
```java
<T extends Comparable & Serializable, U>
```

## 泛型与虚拟机
首先要知道，虚拟机只有普通类，没有泛型类这个概念。也就是说，泛型类最终还是会被解析成普通类

### 类型擦除  
泛型类都会被提供一个原始类型。  
如，没有限定类型的泛型类替换方式
```java
public class Pair<T>{
    private T first;
    ...
}
//类型擦除后,T没有限定类型，会被替换成 Object
public class pair{
    private Object first;
    ....
}
```
如果是有限定类型的话
```java
public class pair<T  extends Comparable & Serializable>{
    private T first;
    ...
} 
//擦除后,会替换为第一个限定类型
public class pair{
    private Comparable first;
    ....
}
//如果是 <T  extends  Serializable& Comparable>
public class pair{
    private Serializable first;
    ....
}
```
如上代码所示，T会替换为第一个限定类型，而编译器在必要时会向另一个限定类型插入强制类型转换。  
所以，为了提高效率，一般把标签接口(即没有方法的接口)放在末尾


## 翻译泛型方法
```java
public static <T extends Comparable 〉 T min ( T[] a )
//擦除类型后
public static Comparable min(Comparable a)
```

## 翻译泛型表达式

假设有如下泛型类

```java
public class Pair<T>{
    private T first;
    public  Pair(T first){
        this.first = first;
    }
    public <T> T getFirst(){return first}
}
```

如我们调用如下方法
```java
Pair<Employee> buddies = ...;
Employee buddy = buddies.getFirst();
``` 
编译器会把这个方法翻译为两条虚拟机指令
1. 第一步：对原始getFirst()的调用((T 擦除后替换成Object，方法返回Object))
2. 第二步：将Object强制转换为Employee

相当于变为原始的方法，再对泛型部分进行强转

即geteFirst方法会变成
```
public Employee getFirst(){
    return (Employee)first;
}
```

如果是setFirst方法的话会变成
```java
public void setFirst(Object first){
    this.first = (Employe)first;
}
```

所以在本质上可以说，泛型是将各种类型变量还原成原始数据，在强转。  
如下例子,虽然不是很好的编程风格，但也是进行了强制转换
```java
Pair<Employee> buddies = ...;
Employee buddy = buddies.first;
```

## 方法擦除带来的问题1：擦除与多态冲突
```java
class ChildClass extends ParentClass<LocalDate>{
    private LocalDate second;`
    public void setSecond(LocalDate second){
        ....
    }
}

class ParentClass <T>{
    public void setSecond(T second){
        ...
    }
}
```

执行以下代码
```java
ChildClass aChild = new ChildClass();
ParentClass<LocalDate> aParent = aChild; //多态
aParent.setSecond(aLocalDate);
```

按照泛型的特点,擦除后

```java
class ChildClass extends ParentClass{
    public void setSecond(LocalDate second){
        ....
    }
}

class ParentClass{
    public void setSecond(Object second){
        ...
    }
}

```
此时可以看到，有两个不同的setSecond()方法 ,而不是完成子类方法覆盖父类方法(很显然，擦除带来的效果与多态的冲突)  

泛型是如何解决多态？  
答：编译器会在ChildClass自动生成一个桥方法，达到类似于多态的效果。  
桥方法类似于：
```java
public void setSecond(Object second){
    setSecond((LocalDate)second)
}
```

同理，如果有如下代码
```java
class ChildClass extends ParentClass<LocalDate>{
    public LocalDate getSecond(){
        ....
    }
}

class ParentClass <T>{
    public <T> T getSecond(){
        ...
    }
}
```
擦除后ＣhildClass会有两个getSecond()方法
```java
class ChildClass extends ParentClass{
    // 自己定义的
    public LocalDate getSecond(){
        ....
    }
    //桥方法
    public Object getSecond(){
        ...
    }
}
```

没错，是真的有两个getSecond方法，在java中可能不合法，在虚拟机中是合法的。

