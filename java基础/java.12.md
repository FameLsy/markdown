---
title: java基础：异常
date: 2018-02-02 00:00:12
tgas: 
- 异常
categories: 
- JavaSe教程
---

Java异常处理通过5个关键字控制:try、catch、throw、throws和 finally

# 异常分类

异常对象都是派生于 Throwable 类的一个实例,但在下一层立即分解为两个分
支 : Error 和 Exception

## Error 
Java 运行时系统的内部错误和资源耗尽错误

## Exception
真正需要关心的异常，分解为两个分支 :
1. RuntimeException :程序错误导致的异常属于 RuntimeException
2. 其他异常 :程序本身没有问题,但由于像I/O错误这类问题导致的异常属于其他异常（ＩＯException）

**RuntimeException包含情况**  
1. 错误的类型转换
2. 数组访问越界
3. 访问到null指针

**其他异常包含情况**  
1. 试图在文件尾部后面读取数据
2. 试图打开一个不存在的文件
3. 试图根据给定的字符串查找 Class 对象, 而这个字符串表示的类并不存在

# 受查异常和非受查异常

非受查异常: 派生于Error类或RuntimeException类的所有异常称为非受查( unchecked)异常  

受查异常：其他的异常称为受查 ( checked ) 异常

# 声明受查异常（htrows）

如果是受查异常，应该在方法首部声明所有可能抛出的异常(通过关键字throws)  

如下是Java类库中的一个类的构造器方法的声明
```java
public FilelnputStream ( String name ) throws FileNotFoundException
```

什么时候应该声明异常？
1. 调用一个抛出受査异常的方法
2. 程序运行过程中发现错误 , 并且利用 throw 语句抛出一个受查异常
3. 对于Java 的内部错误（Error），不必声明
4. RuntimeException,不必声明
5. 总结：方法必须抛出所有可能抛出的受查异常，非受查异常要么不可控制(Error)要么就应该避免发生(RuntimeException)

## 子类异常和父类异常 

1. 在子类中覆盖了父类的一个方法 ,子类方法中声明的受查异常不能比父类方法中声明的异常更通用(子类方法中可以抛出更特定的异常, 或者根本不抛出任何异常)
2. 父类类方法没有抛出任何受查异常, 子类也不能抛出任何受查异常

# 抛出异常(throw)

如抛出一个EOFException异常(IOException子类)
```java
throw EOFException();
```
别忘记在方法首部声明抛出的受查异常

# 捕获异常(try/catch)
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

## 捕获多个异常  

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

## 合并catch

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
# finally子句

可以这样使用,有异常抛出就直接执行finally中的语句
```java
try{

}finally{

}
```

## 建议解耦try/catch和try/finally

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

## finally与return 

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

## finally抛出异常带来的麻烦

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

# 带资源的try语句(Java SE 7)
try-with-resource最简形式：
```java
try (Resource res = ...；Ｒesource res2 = ...){
    ...
}
```
当try块退出时，自动执行res.closed()。  

对于colosed()可能抛出的异常，原来的异常会被抛出，而closed()异常会被自动捕获,并由Throwable.addSuppressed()添加到原来的异常中。（可以使用Throwableget.Suppressed()获取。）


# 自定义异常
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
## Throwable 定义的方法
Exception类自己没有定义任何方法。,它继承了Throwable提供的一些方法  

|方法| 描述|
|--|--|
|Throwable fillInStackTrace( )| 返回一个包含完整堆栈轨迹的Throwable对象,该对象可能被再次引发|
|String getLocalizedMessage( )| 返回一个异常的局部描述|
|String getMessage( ) |返回一个异常的描述|
|void printStackTrace( ) |显示堆栈轨迹|
|void printStackTrace(PrintStreamstream) |把堆栈轨迹送到指定的流|
|void printStackTrace(PrintWriterstream)| 把堆栈轨迹送到指定的流|
|String toString( )| 返回一个包含异常描述的String对象。当输出一个Throwable对象时,该方法被println( )调用|

# Java的内置异常
Java 的 java.lang 中定义的未检查异常子类:

|异常|说明|
|--|--|
|ArithmeticException |算术错误,如被0除|
|ArrayIndexOutOfBoundsException |数组下标出界|
|ArrayStoreException| 数组元素赋值类型不兼容|
|ClassCastException| 非法强制转换类型|
|IllegalArgumentException| 调用方法的参数非法|
|IllegalMonitorStateException |非法监控操作,如等待一个未锁定线程|
|IllegalStateException |环境或应用状态不正确|
|IllegalThreadStateException| 请求操作与当前线程状态不兼容|
|IndexOutOfBoundsException| 某些类型索引越界|
|NullPointerException| 非法使用空引用|
|NumberFormatException| 字符串到数字格式非法转换|
|SecurityException| 试图违反安全性|
|StringIndexOutOfBounds| 试图在字符串边界之外索引|
|UnsupportedOperationException| 遇到不支持的操作|

java.lang 中定义的检查异常:

|异常|说明|
|--|--|
|ClassNotFoundException |找不到类|
|CloneNotSupportedException| 试图克隆一个不能实现Cloneable接口的对象|
|IllegalAccessException |对一个类的访问被拒绝|
|InstantiationException| 试图创建一个抽象类或者抽象接口的对象|
|InterruptedException| 一个线程被另一个线程中断|
|NoSuchFieldException |请求的字段不存在|
|NoSuchMethodException| 请求的方法不存在|


# 分析堆栈轨迹元素
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

# 使用异常机制技巧
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

