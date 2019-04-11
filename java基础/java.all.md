---
title: java 基础
date: 2018-02-02 00:00:01
tags: 
- Java基础
categories:
- Java
---


<img src="https://raw.githubusercontent.com/FameLsy/Images/master/Fullmetal%20Alchemist/wallhaven-382616.jpg" width = "900" height = "600" alt="git" align=center />
<!-- more -->

# java 数据类型

》 java一共有8个基本数据类型

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

》 JAVA范围与j运行ava代码机器无关,因为Java程序必须保证在所有机器上都能够得到相同的结果，所以各种数据类型的取值范围必须固定。

## 整数类型

|数据类型|存储需求|取值范围|前/后缀|
|--|--|--|--|
|long|8字节|-2^64~2^64 -1|后缀：L或l,如40000L|
|int| 4字节|-2^32~ 2^32-1||
|short|2字节|-2^16~ 2^16 -1||
|byte|1字节|-2^8 ~ 2^8 -1||

## 字节型(byte)

》 最小的整数类型是字节型。它是有符号的8位类型,数的范围是-128~127。当你从网络或文件处理数据流的时候,字节类型的变量特别有用。

## 短整型(short)

》 有符号的16位类型,被定义为高字节优先(称为big-endian格式),它可能是Java中使用得最少的类型。主要适用于16位计算机。

## 整型(int)

》 有符号的32位类型。int类型的变量通常被用来控制循环及作数组的下标。任何时候你的整数表达式包含byte,short,int及字面量数字,在进行计算以前,所有表达式的类型被提升(promoted)到整型。

》 使用字节型和短整型可以节约空间,但是不能保证 Java 不会内部把那些类型提升到整型；记住，类型决定行为,而不是大小(惟一的例外是数组,字节型的数据保证每个数组元素只占用一个字节,短整型使用 2 个字节,整型将使用4个。)

## 长整型(long)

》 有符号的64位类型,长整型数的范围是相当大的。这使得大的、整个数字都被需要时,它是非常有用的

## 单精度浮点型(float)
》 单精度浮点型(float)专指占用32位存储空间的单精度(single-precision)值。  
》 当值很大或很小的时候,它将变得不精确。当你需要小数部分并且对精度的要求不高时,单精度浮点型的变量是有用的。

## 双精度型(double)浮点型

》 占用64位的存储空间  

|特殊值|对应常量|
|--|--|
|正无穷大|Double.POSITIVE_INFINITY 、 Float.POSITIVE_INFINITY|
|负无穷大|Double.NEGATIVE_INFINITY、Float.NEGATIVE_INFINITY|
|NaN(不是一个数)|Double.NaN、Float.NaN|
  
》 浮点类型不适用于无法接受舍入误差的金融计算中，如2.0 -1.1 会输出0.8999999999999999，而不是0.9  
》》 原因：浮点数值采用二进制系统表示法（二进制无法精确表示分数1/10）  
》》 如果不允许输入误差，需要使用 *BigDecimal*类


## 布尔型（boolean）

》 表示逻辑值的简单类型，它的值只能是真或假这两个值中的一个


## 数值类型转换

》 分为**自动类型转换**和**强制类型转换**。

### 自动类型转换

》 满足以下条件将自动转换
1. 这2种类型是兼容的。
2. 目的类型数的范围比来源类型的大。

》 当以上2个条件都满足时,拓宽转换(widening conversion)发生  
》 数字类型和字符类型(char)或布尔类型(bollean)是不兼容的。字符类型(char)和布尔类型(bollean)也是互相不兼容的。

### 强制转换


```java
(target-type)value
```

》 截断(truncation):把浮点值赋给整数类型时一种不同的类型转换,小数部分会被舍去。

》 java 257强制转换赋值给byte会怎么样？323呢?  
》》 答：byte范围是256,所以257赋值后，值为1(257/256)。323为67(323/256)

### 基本数值类型直接的转换

》 虚线表示数据可能丢失,如果对图中箭头反向转换，会报错，需要强制转换

![数值类型转换](https://raw.githubusercontent.com/FameLsy/Images/master/javase/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20190106130122.png)

## 表达式中的类型提升

》 所有byte型和short型的值在表达式中会被提升到int类型  
1. 好处：避免了计算过程中中间结果超出范围
2. 缺点，需要强转

》 类型提升规则：
1. 如果两个操作数中有一个是 double 类型， 另一个操作数就会转换为 double 类型。
2. 否则， 如果其中一个操作数是 float 类型， 另一个操作数将会转换为 float 类型。
3. 否则， 如果其中一个操作数是 long 类型， 另一个操作数将会转换为 long 类型。
4. 否则， 两个操作数都将被转换为 int 类型

```java
byte a = 50;
byte b = 20;
int i = b * a; //ok
b = b * 2;//error,cannot assign an int to a byte
b = (byte)b; //ok
```



## 对象包装器和自动装箱

》 基本类型对应的类，被称为**包装器**。

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

》 前六个包装器继承于**Number类**。  
  
```java
//不能携程ArrayList<int>
ArrayList<Integer>
```

》 **装箱**和**拆箱**是编译器认可的，而不是虚拟机（编译器在生成类的字节码的时候，插入必要的方法调用，而虚拟机这是执行这些字节码

### 自动装箱

》 将基本数据赋值给包装器
```java
list.add(3);
//自动装箱成
list.add(Integer.valueOf(3));
```
 ### 自动拆箱

 》 将包装器对象赋值给基本数据
 ```java
 int n = list.get(i);
 //自动拆箱
 int n = list.get(i).intValue();
 ```

### 混用Integer和Double

》 Integer会先自动拆箱，提升为Double，在装箱成Double
```java
Integer n = 1;
Double x = 2.0;
System.out.println(true ? n : x);
```

# 变量

》 变量是Java程序的一个基本存储单元,变量由一个标识符,类型及一个可选初始值的组合定义。
所有的变量都有一个作用域,定义变量的可见性,生存期。

## 声明变量

》 基本的变量声明方法如下

```java
//type: Java的基本类型之一,或类及接口类型的名字  
//标识符(identifier): 变量的名字,指定一个等号和一个值来初始化变量。
type identifier [ = value][,identifier [= value] ...] ;
```

## 变量命名规范  

》 需要注意的是 **$** 虽然合法，但一般不使用在自己的代码中，只用于Java编译器或其他工具生成的名字中;对于讲变量名命名为类型名，许多程序员喜欢加上前缀"a",如Box abox
```
字母[a~zA~Z_$]
```

## 变量的作用域和生存期
》 程序块被包括在一对大括号中。一个程序块定义了一个作用域(scope)。变量在其作用域内被创建,离开其作用域时被撤消  

# 常量

》 利用关键字final指示常量，只能赋值一次,且常量名习惯上使用全大写加 *_*的方式命名  

## 类常量

》 通过关键字 *static final* 设置的常量

# 数组

## 数组初始化

```java
//自动初始化值
new type[size];
//自定义初始化值
int[] smallPrimes = { 2, 3, 5, 7, 11, 13 };
```

## 初始化匿名数组

```java
new int[] { 17, 19, 23, 29, 31, 37 }
```

## 多维数组

```java
int twoD[][] = new int[4][5];
// 也可以,第二维手工分配    
int twoD[][] = new int[4][];
twoD[0] = new int[5];
twoD[1] = new int[5];
twoD[2] = new int[5];
twoD[3] = new int[5];
```

# 运算符

## 算数运符  

|运算符| 含义|
|--|--|
|+| 加法|
|– |减法(一元减号)|
|* |乘法|
|/| 除法|
|% 模运算|
|++| 递增运算|
|+= |加法赋值|
|–= |减法赋值|
|*= |乘法赋值|
|/= |除法赋值|
|%= |模运算赋值|
|-- |递减运算|

## 位 运 算 符

|运算符| 结果|
|--|--|
|~| 按位非(NOT)(一元运算)|
|&| 按位与(AND)|
|\|| 按位或(OR)|
|^| 按位异或(XOR)|
|>>| 右移,被移走的最高位(最左边的位)由原来最高位的数字补充|
|>>>| 右移,左边空出的位以0填充|
|<< |左移,高阶位都被移出(并且丢弃),并用0填充右边|
|&=| 按位与赋值|
|\|=| 按位或赋值|
|^=| 按位异或赋值|
|>>=| 右移赋值|
|>>>=| 右移赋值,左边空出的位以0填充|
|<<=| 左移赋值|

## 关系运算符

|运算符| 意义|
|--|--|
|==| 等于|
|!= |不等于|
|>| 大于|
|< |小于|
|>=| 大于等于|
|<=| 小于等于|

## 布尔逻辑运算符

布尔逻辑运算符的运算数只能是布尔型。而且逻辑运算的结果也是布尔类型

|运算符 |含义|
|--|--|
|& |逻辑与|
|||逻辑或|
|^ |异或|
|||| 短路或|
|&&| 短路与|
|!| 逻辑反|
|&= |逻辑与赋值(赋值的简写形式)|
|\|= |逻辑或赋值(赋值的简写形式)|
|^= |异或赋值(赋值的简写形式)|
|==| 相等|
|!= |不相等|
|?:| 三元运算符(IF-THEN-ELSE)|

## 数学函数

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

## 自增自减运算
++、--

## 关系运算符

|| , &&

## 三元运算符**

```java
condition ? expression1 : expression2;
```

## 位运算

$、|、^、~、>>（左移,使用符号位填充高位）、<<（右移）、>>>(使用0填充高位)

# 流程控制语句

## 选择语句

### if语句

```java
if (condition) statement1;
else statement2;
```

### switch语句

》 需满足:
1. 表达式expression必须为byte,short,int或char类型。  
2. 每个case语句后的值value必须是与表达式类型兼容的特定的一个常量(它必须为一个常量,而不是变量)。  
3. 重复的case值是不允许的。  
4. 如果没有break语句,程序将继续执行下面的每一个case语句,直到遇到break语句(或switch语句的末尾)

```java
switch (expression) {
    case value1:
    // statement sequence
    break;
    case value2:
    // statement sequence   
    break;
    .
    .
    .
    case valueN:
    // statement sequence
    break;
    default:
    // default statement sequence
}
```

## 循环语句

### while语句

》 while语句在循环一开始就计算条件表达式,若开始时条件为假,则循环体一次也
不会执行

```java
while(condition) {
// body of loop
}
```

### do-while循环

》 do-while循环总是先执行循环体,然后再计算条件表达式，所以至少一次

```java
do {
// body of loop
} while (condition);
```


### for循环

```java
for(initialization; condition; iteration) {
// body
}
```
Java允许你在for循环的初始化部分和反复部分声明多个变量
```java
for(a=1, b=4; a<b; a++, b--) {
    System.out.println("a = " + a);
    System.out.println("b = " + b);
}
```

## 跳转语句

### break语句

》 作用：
1. 在switch语句中,它被用来终止一个语句序列
2. 能被用来退出一个循环
3. 能作为一种“先进”的goto 语句来使用

**break不是被设计来提供一种正常的循环终止的方法!!!**

把 break 当作 goto 的一种形式来用,标签break语句的通用格式
```java
label:{
    ...
    break label//直接跳出循环，调到label块的尾部执行some code..
}
// some code..

```
### continue语句

》 跳出当前循环，开始新的一次循环

### return语句
》 return语句用来明确地从一个方法返回

# 继承

》 关键字 **extends** 表示继承

## 成员的访问与继承

》 子类方法不能够直接地访问超类的私有域。需要通过super调用父类的getter方法。  
》 子类继承了父类的所有属性和方法或子类拥有父类的所有属性和方法，只不过父类的私有属性和方法，子类是无法直接访问到的。即只是拥有，但是无法使用

## super

### 用法一：作为引用的关键字

》 子类可以通过spuer调用超类中定义的构造函数方法

```java
super(parameter-list);
```

》 super( )必须是在子类构造函数中的第一个执行语句。  
》 super 不是一个对象的引用， 不能将 super 赋给另一个对象变量， 它只是一个指示编 译器调用超类方法的特殊关键字。  

### 用法二：类似于this的用法

```java
//member既可以是1个方法也可以是1个实例变量
super.member
```

## 方法重写(override）

》 如果子类中的一个方法与它超类中的方法有相同的方法名和类型声明,称子类中的方法重写(override)超类中的方法

》 方法重写应遵循“三同一小一大”原则：  
1. “三同”：即方法名相同，形参列表相同，返回值类型相同；  
2. “一小”：子类方法声明抛出的异常比父类方法声明抛出的异常更小或者相等；  
3. “一大”:子类方法的访问修饰符应比父类方法更大或相等

## 子类构造器

》 因为子类不能直接访问父类的私有域，所以必须利用父类的构造器对部分私有域进行初始化。  通过 super 实现对超类构造器的调用。super 调用构造器的语句必须是子类构造器的第一条语句。  
》 子类的构造器没有显式地调用超类的构造器， 则将自动地调用超类默认（没有参数 ) 的构造器。  如果超类没有不带参数的构造器， 并且在子类的构造器中又没有显式地调用超类 的其他构造器’ 则 Java 编译器将报告错误  

## 多态

》 一个对象可以指示多种实际类型的现象被成为**多态**。
》 当一个子类对象的引用被赋给一个超类引用变量时,你只能访问超类定义的对象的那一部分(即由父类引用子类对象，无法调用子类的特有方法。)
》子类引用父类对象，需要强转。  

### 动态绑定

》 动态方法调度是一种在运行时而不是编译时调用重载方法的机制,即在运行时能够自动地选择调用哪个方法的现象被称为动态绑定，这是Java实现运行时多态性的基础



### 理解方法调用

》 当一个重载方法通过超类引用被调用,Java根据当前被引用对象的类型来决定执行哪个版本的方法

》 调用过程的详细描述：
1. 编译器査看对象的声明类型和方法名。【编译器已获得所有可能被调用的候选方法，同名不同参数的方法】  
2. 接下来，编译器将査看调用方法时提供的参数类型【编译器已获得需要调用的方法名字和参数类型。】。编译器没有找到与参数类型匹配的方法， 或者发现经过类型转换后 有多个方法与之匹配， 就会报告一个错误。
3. 如果是 private 方法、 static 方法、 final 方法或者构造器， 那么编译器将可以准确地知道应该调用哪个方法， 我们将这种调用方式称为静态绑定（ static binding )。
4. 当程序运行，并且采用动态绑定调用方法时， 虚拟机一定调用与 x 所引用对象的实 际类型最合适的那个类的方法。。假设 x 的实际类型是 D，它是 C 类的子类。如果 D 类定义了 方法 f(String，) 就直接调用它；否则， 将在 D 类的超类中寻找 f(String，) 以此类推。每次调用方法都要进行搜索，时间开销相当大。因此，虚拟机预先为每个类创建了一个 方法表（ method table), 其中列出了所有方法的签名和实际调用的方法。这样一来，在真正 调用方法的时候， 虚拟机仅查找这个表就行了

# 修饰符

## static

》 声明为static的变量实质上就是全局变量

》 声明为static的方法有以下几条限制
1. 它们仅能调用其他的static方法。
2. 它们只能访问static数据。
3. 它们不能以任何方式引用this或super

### 静态域和静态方法

》 静态域（类域）：属于类，而不属于任何独立的对象，所有对象共享。　　

》 静态方法：一种不能向对象实施操作的方法，没有隐式参数  

》 静态常量: static final修饰

### 使用静态方法的情况

》 方法不需要访问对象状态，其所需参数都是通过显式参数提供（例如：Math.pow（））

》  一个方法只需要访问类的静态域

## final

### 作用一：定义常量

》  变量可以声明为final,这样做的目的是阻止它的内容被修改  

》  final域：构建对象时必须初始化这样的域。也就是说，必须确保在每一个构造器执行之后，这个域的值被设置，并且在后面的操作中，不能够再对它进行修改

### 作用二：使用final阻止重写

》  声明成final的方法不能被重载

### 作用三：使用final阻止继承

》  声明成final的类不能被继承

## 访问控制（作用域）

|作用域|当前类|同一package|子孙类|其他package|描述|
|--|--|--|--|--|--|
|public|√|√|√|√|对所有类可见|
|protected|√|√|√|×|对本包和所有子类可见|
|default|√|√|×|×|对本包可见,默认|
|private|√|×|×|×|仅对本类可见|

》  对于类来说，只有默认和public两种访问级别

# Object类

》 Object 类是 Java 中所有类的始祖， 在 Java 中每个类都是由它扩展而来的。

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

》 在 Java 中，只有基本类型 （ primitive types) 不是对象， 例如，数值、 字符和布尔类型的 值都不是对象。

## equals 方法

》 Object 类的equals方法判断两个对象是否具有相同的引用。  

》 Java语言规范下的equals
1. 自反性：对于任何非空引用x，x.equals(x)应该返回true
2. 对称性：对于任何引用x和y，当且仅当y.equals(x)返回true，x.equals(y)也应该返回true
3. 传递性：对于任何引用x、y、z，如果x.equals(y)返回true，y.equals(z)返回true，x.equals(z)也应该返回true
4. 一致性：如果x和y引用的对象没有发生变化，反复调用x.equals(y)应该返回同样的结果
5. 对于任何非空引用x，x.equals(null)应该返回true

》 完美equals建议

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

》 散列码（ hash code ) 是由对象导出的一个整型值，其值为对象的 ***存储地址**  
》 如果重新定义 equals方法，就必须重新定义 hashCode 方法， 以便用户可以将对象插入到散列表中  

```java
//这是一个由name、salary、hireDay组成的hashCode编码。
public int hashCode(){
    return Objects.hash(name, salary, hireDay)
}
```

# 反射

》 大量应用于JavaBeans中，是Java组件的体系结构,反射可以用来:
1. 在运行时分析类的能力
2. 在运行时查看对象，如编写一个toSring方法供所有类使用
3. 实现通用的数组操作代码
4. 利用Method对象

## Class 类

》 Class类：Java运行时系统始终为所有的对象维护一个被成为运行时的类型标识。这个信息跟踪每个对象所属的类，保存这些信息的类被称为Class。

》 获取Class对象：
```java
//方法1：静态方法(应该提供一个异常处理器)
Class.forName(className);
//方法2：Object的getClass()方法
e.getClass();
//方法3：
Class cl1 = int.class;//int不是类，但int.class是一个类
Class cl2 = Double[].class
```

》 返回类名：
```java
Object.getClass().getName();
```

》 Class类比较：  虚拟机为每个类型管理一个Class对象。所以class比较只需要用==即可

》 创建实例：
```java
e.getClass().newInstance();
//forName()配合使用
String s = "java.util.Random";
Object m = Class.forName(s).newInstance();
```

》 类的结构，java.lang.reflect中的三各类

|类|描述|
|--|--|
|Field|类的域|
|Method|类的方法|
|Constructor|类的构造器


## 在运行时使用反射分析对象

》 查看数据域的实际内容：，其中，Object obj 是一个包含该域的对象。

```java
Field.get(Object obj)
```

》 实例:  
```java
Employee maorl = new Employee("maorl", 39999, 10, 1, 1994);
Class aClass = maorl.getClass();
// 在这里，name是一个私有域，因为反射机制的默认是受Java访问控制的，所以get()方法会抛出一个IllegalAccessException。  
Field aField = aClass.getDeclareField("name");
//获取具体值
Object value = aField.get(masorl);
```

》 如果一个Java程序没有受到安全管理器的控制，就可以覆盖访问控制。为了达到这个目的，可以使用Field、Method、Constructor对象的 *setAccessible*
```java
// 此时，我们可以访问得到name域的值了。  
aFiled.setAcessible(true)
```


## get()自动打包

》 对于get()方法如果域是一个String,那么返回成Object是没有问题的。但如果是一个基本数据类型，get()方法会自动将值打包成对象。

###设置域

》 get()方法用来获取域的值，那么对应的，set(Obj, value)方法用来设置域值
```java
aFiled.set(maosrl, "newName");
```

## 使用反射编写泛型数组代码

》 如何自己编写一个通用数组拷贝的的方法呢？

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

》 以下是Arrays.copyOf()的方法  
1. copyOf()两个参数的方法调用了三个参数的方法，把原始的数组类传了进去。  
2. 通过三元运算符判断原始数据是否属于Object[]数组  
    是Object就直接new Object[]  
    否则需要调用Array.newInstance方法来创建新数组

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


## 调用任意方法

》 使用Method.invoke(Object obj, Object... args)进行调用  
 第一个obj为隐式参数(静态方法就传null,有点类似于feild.get()方法)  
 第二个数组对象为显示参数（在JavaSE 5.0以前必须传递，没有显示参数就传0）  

》因为其返回类型为Object，所以需要进行相应的类型转换 

》如果返回值是基本数据类型，会自动包装
```java
    public static void main(String[] args) throws Exception {
        Employee maosrl = new Employee("maosrl", 1000);
        Method toString = Employee.class.getMethod("toString");
        System.out.println((String)toString.invoke(maosrl));
    }
```


## 通过反射打印类的信息

```java
import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.Method;
import java.lang.reflect.Modifier;
import java.util.Scanner;

public class ReflectionTest {
    public static void main(String[] args) {
        String name;
        if (args.length > 0 ) name = args[0];
        else {
            Scanner in = new Scanner(System.in);
            System.out.println("输入Class名");
            name = in.next();
        }

        /**
         * 通过Class.forName获取类
         * 通过Class.getSuperclass()获取父类
         * 通过Modifier.toString(aClass.getModifiers()) 获取类的修饰符
         */
        try {
//            打印类名、修饰符
            Class<?> aClass = Class.forName(name);
            Class<?> superclass = aClass.getSuperclass();
            String modifiers = Modifier.toString(aClass.getModifiers());
            if (modifiers.length() > 0) System.out.print( modifiers + "");
            System.out.print(" class " + name);
//            打印父类
            if (superclass != null && superclass != Object.class) System.out.print(" extends " + superclass.getName());
//            打印构造器、方法、域
            System.out.print("\n{\n");
            printConstructors(aClass);
            System.out.println();
            printMethods(aClass);
            System.out.println();
            printFields(aClass);
        }catch (ClassNotFoundException e){
            e.printStackTrace();
        }
        System.exit(0);
    }
    /**
     * 打印类的所有构造器
     *
     * 通过Class.getConstructors方法获取构造器数组
     * 通过Modifier.toString(Constructor.getModifiers()) 方法获取构造器的修饰符
     * 通过Constructor.getParameterTypes() 方法获取构造器参数
     * @param aClass
     */
    public static void printConstructors(Class aClass) {
        Constructor[] constructors = aClass.getConstructors();

//        打印构造器名和修饰符
        for (Constructor constructor : constructors) {
            String name = constructor.getName();
            System.out.print("  ");
            String modifiers = Modifier.toString(constructor.getModifiers());
            if (modifiers.length() > 0) System.out.print(modifiers + " ");
            System.out.print(name + "(");

//            打印参数类型
            Class[] parameterTypes = constructor.getParameterTypes();
            for (int j = 0; j < parameterTypes.length; j++) {
                if (j > 0) System.out.print(",");
                System.out.print(parameterTypes[j].getName());
            }
            System.out.println(");");
        }
    }

    /**
     * 打印类的所有方法
     *
     * 通过Class.getDeclaredMethods()获取类的所有方法
     * 通过Method.getReturnType(); 获取方法的返回类型
     * 通过Modifier.toString(Method.getModifiers()) 方法获取方法的修饰符
     *  通过Method.getParameterTypes() 方法获取构造器参数
     * @param aClass
     */
    public static void printMethods(Class aClass){
            Method[] methods = aClass.getDeclaredMethods();

//            打印类方法名、修饰符、返回类型
            for(Method method : methods){
                Class<?> type = method.getReturnType();
                String name = method.getName();
                System.out.print(" ");
                int modifiers1 = method.getModifiers();
                String modifiers = Modifier.toString(modifiers1);
                if (modifiers.length() > 0) System.out.print(modifiers + " ");
                System.out.print(type.getName() + " " + name + "(");

                //打印参数类型
                Class<?>[] parameterTypes = method.getParameterTypes();
                for (int j = 0; j < parameterTypes.length; j++){
                    if (j > 0) System.out.print(",");
                    System.out.print(parameterTypes[j].getName());
                }
                System.out.println(")");
            }
    }

    /**
     * 通过Class.getDeclaredFields()获取类的所有域
     * 通过Field.getType()获取域的类型
     * 通过Modifier.toString(field.getModifiers()) 获取域的修饰符
     * @param aClass
     */
    public static void printFields(Class aClass){
        Field[] fields = aClass.getDeclaredFields();

        for (Field field : fields){
            Class<?> type = field.getType();
            String name = field.getName();
            System.out.print(" ");
            String modifiers = Modifier.toString(field.getModifiers());
            if (modifiers.length() > 0) System.out.print(modifiers + "");
            System.out.println(type.getName() + " " + name + ";");
        }
    }


}
```

##  利用反射编写通用toString，打印对象的域值

》 ObjectAnalyzer类:
```java
import java.lang.reflect.AccessibleObject;
import java.lang.reflect.Array;
import java.lang.reflect.Field;
import java.lang.reflect.Modifier;
import java.util.ArrayList;

public class ObjectAnalyzer {

    private ArrayList<Object> visited = new ArrayList<>();

    /**
     * 打印对象域的值
     * 1. Null 值：直接返回"null"
     * 2. ArrayList visited：防止无限递归
     * 3. 获取对象的类
     * 4. 如果是String类，直接返回数值即可
     * 5. 如果是数组类
     *       获取数组的组件类：Class.getComponentType()方(如 int[]对象，返回int.class, Integer[] 返回 Integer.class)
     *       遍历数组，分两种情况
     *       1）对于基本类型数组：Class.isPrimitive()判断是否属于基本类型的类,可以直接处理值
     *       2) 对于对象数组：则需要递归使用toString()来处理其中的域值
     * 6. 如果是对象类，先要获取它的域值：
     *       1）获取所有域：Class.getDeclaredFields()；
     *       2）要获取值，使用AccessibleObject.setAccessible(Field[] field, true); 来批量接解除的访问控制
     *       3）遍历域：
     *          I：获取域的类型，对于基础类型，可以直接处理值；对于对象类型，需要再次toString来递归处理域值
     *          II：除了当前对象的域值外，还需要处理它的父类，Class.getSuperclass()
     *
     *
     * @param obj
     * @return
     */
    public String toString(Object obj){
        if(obj == null) return "null";
        if (visited.contains((obj))) return "...";
        visited.add(obj);

        Class<?> aClass = obj.getClass();

        //处理String类
        if(aClass == String.class) return (String) obj;

        //处理数组
        if (aClass.isArray()){
            String result = aClass.getComponentType() + "[]{";
            for (int i = 0; i < Array.getLength(obj); i++){
                if (i > 0) result += ",";
                Object val = Array.get(obj, i);
                //isPrimitive 判断该类是否是一个基本类型(即int、double...)
                if (aClass.getComponentType().isPrimitive()) result += val;
                else result += toString(val); //不是基础类型还需要再次调用toString方法进行转换
            }
            return result + "}";
        }

        String result = aClass.getName();
        do{
            Field[] fields = aClass.getDeclaredFields();
            AccessibleObject.setAccessible(fields, true);

            for (Field field : fields){
                if (!Modifier.isStatic(field.getModifiers())){
                    if (!result.endsWith("[")) result += ",";
                    result += field.getName() + "=";
                    try {
                        Class<?> type = field.getType();
                        Object val = field.get(obj);
                        if (type.isPrimitive()) result += val;
                        else result += toString(val);
                    }catch (Exception e){
                        e.printStackTrace();;
                    }
                }
            }
            result += "]";
            aClass = aClass.getSuperclass();
        }
        while (aClass != null);
        return result;

    }
}
```
》 测试类:
```java
import java.util.ArrayList;

public class ObjectAnalyzerTest {
    public static void main(String[] args) {
        ArrayList<Integer> squares = new ArrayList<>();
        for (int i = 1; i <= 5; i++) squares.add(i * i);
        int[] ints = {1, 2, 4, 5};
        System.out.println(new ObjectAnalyzer().toString(squares));
        System.out.println(new ObjectAnalyzer().toString(ints));
    }
}
```

# 接口

》 接口方法都是public,可以不写。但是实现接口的时候必须写（实现类不写是default）


## 接口特性

》 接口的特性如下
1. 无法实例化
2. 能声明接口的变量
3. 可以使用 instanceof 检查类是否属于某个特定的接口
4. 接口可以继承接口
5. 接口不能包含实例域和静态方法(java 8.0后可以添加静态方法)
6. 接口可以包含常量（共享，因为是public static final）
7. 接口方法自动设置为public abstract,接口域自动设置为public static final

## 默认方法

》 可以为接口方法提供一个默认方法,使用default 修饰符标记

```java
public interface Comparable<T>{
    default int compareTo(T other){return 0;}
}
```

### 应用一：

```java
//把所有方法都设置为默认，程序员只需覆盖关心的方法即可
public interface Demo(){
    default void method1(){}
    default void method2(){}
    default void method3(){}
    default void method4(){}
    default void method5(){}
}
```

### 应用二：接口演化

》 假设我们现在有一个接口 A, Mas是其的一个实现类

```java
public interface A{...}
publiic Class Max implements A{...}
```

》 后来，我们为A添加了一个新方法 newMethod

```java
public interface A{
    void newMethod(){}
}
```

》 可能会出现以下情况  
1. Mas类编译将会失败（没有重写newMethod）
2. 不重新编译Mas类，在Mas调用newMethod时抛出AbstractMethodError

》 如果变为defalut方法，以上两个问题都会解决  
1. Mas成功编译
2. 不重新编译Mas,调用时会向上调用A接口的newMethod


## 解决默认方法冲突
　
### 情况一：接口与父类冲突

》 父类优先  如接口定义了默认方法getName(),而父类中也定义了getName()方法，默认方法被忽略  

### 情况二：接口与接口冲突：  

》  手动覆盖。如接口A、Ｂ同时定义了默认的getName()方法，必需覆盖解决冲突。 
也可以在类中选择二者其一

```java
Class Choose implements A,B{
    public String getName(){
        return A.super.getName();
    }
}　
```

### 情况三：非默认

》 如果接口Ａ、Ｂ的方法是非默认方法，无论是否另一个有同名默认方法，必需重写


# 异常

》 异常对象都是派生于 Throwable 类的一个实例,但在下一层立即分解为两个分
支 : **Error** 和 **Exception**

