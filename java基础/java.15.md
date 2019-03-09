---
title: java基础：泛型
date: 2018-02-02 00:00:15
tgas: 
- 泛型
categories: 
- JavaSe教程
---

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
```java
public Object getFirst(){
    return first;
}
//再进行强转
(Employee)buddies.getFirst();
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

## 方法擦除带来的问题
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

## 调用遗留代码
对于较早期的代码，当时没有泛型这个概念  
```java
public void set(MyClass class);
public MyClass get();
```
如果将一个泛型参数传入
```java
MyClass<String> aClss = ...;
set(aClass);
```
或者将一个原始类型赋值给泛型变量
```java
MyClass<Class> aClass = get();
```
都会方法一个警告，最差的情况就是抛出一个类型转换错误  
可以使用注解忽略警告
```java
@SuppressWarnings("unchecked")
public void set(MyClass class);
@SuppressWarnings("unchecked")
public MyClass get();
```

# 泛型约束与局限性

## 不能使用基本数据类型实例化类型参数
没有
```java
Pair<double>
```
只有
```java
Pair<Double>
```
原因，类型擦除后含有Object域，而Object不能存储double

## 运行时类型查询只适用于原始类型

因为虚拟机里是没有泛型类型的，所以类型查询只生产原始类型  

视图查询一个对象是否属于某个泛型类型时，会得到一个编译器错误
```java
if(a instanceof Pair<String>) //error
if(a instanceof Pair<T>)  //error
```

而使用强制转换类型，会得到一个警告
```java
Pair<String> aPair = (Pair<String>)a;//warning
```

getClass()方法也只返回原始类型
```java
Pair<String> aPairString = ...;
Pair<Double> aPairDouble = ...;
if(aPairString.getClass() == aPairDouble.getClass()) //true
```

## 不能实例化参数化类型的数组

如,可以定义Pair< String>[] table 变量，但不能使用new Pair< String>[10]实例化。
```java
Pair<String> [] table = new Pair<String>[10] //error
```
原因:  
假设可以创建，那擦除后代码如下
```java
Pair[] table = new Pair[10];
```
会发现，擦除过后，泛型机制将会无效,只要是一个Pair类就可以传入数组
```java
table[0] = new Pair<Double>();
```
那么我们在原有的定义Pair< String>下使用数据，肯定会出现类型错误

>注  
> 如果真的需要保存参数类型化对象，可以使用ArrayList<Pair< String>>

## Varargs(可变参数) 警告
如下方法
```java
public static <T> void addAll(Collection<T> aCollection, T... ts){
    ...
}
```
要是传入多个参数
```java
Pair<String> aPair1 = ...;
Pair<String> aPair2 = ...;
addAll(aCollection, aPair1, aPair2);
```
对于aPair1、aPair2，虚拟机必须建立一个Pair<String>[]数组。而java是不能实例化参数化类型的数组。  
这种情况下，规则会有所放松，只会得到一个警告，而不是错误。  
想要忽视这个警告，由以下两种方式：
1. @SuppressWarning("unchecked')注释
2. @SafeVarargs 注释(Java SE 7.0)

## 不能实例化类型变量
不能使用以下表达式
```java
new T(...);//error
new T[...]//error
T.class; //eror
```

如下构造器也是不合法的
```java
public Pair(){
    first = new T();
    second = new T();
} //error
```
对于构造器，Java SE 8后，最好的解决办法是让调用者提供一个构造器表达式
```java
Pair<String> p = Pair.makePair(String::new);
//Supplier<T>是一个函数接口，表示一个无参而且返回值类型为T的函数
public static <T> Pair<T> makePair(Supplier<T> constr){
    rerturn new Pair<>(constr.get(), constr.get())
}
```
传统的解决方法，是通过反射调用Class.newInstance方法来构造泛型对象
```java
//注意：Class<T>本身也是泛型，String.class就是Class<String>
public  static <T> Pair<T> makePair(Class<T> aClass){
    try{
        return new Pair<>(aClass.newInstance(), aClass.newInstance());
    }catch(Exception e){
        return null;
    }
}
```

## 不能构造泛型数组

## 泛型类的静态上下文种类型变量无效

禁止使用带有类型变量的静态域和静态方法
```java
public class MyClSS<T>{
    public static T field;//error
    public static  T get(){...};//error
}
```

## 不能抛出或捕获泛型类的实例

泛型类不能被抛出，也不能被捕获，甚至无法扩展Throwable类(Throwable可以被抛出捕获)
```java
public class MyException<T> extends Exception{...}//error,编译不通过
```
catch子句不能使用类型变量(即不能被捕获)
```java
catch(T t){...}//error
```

不过，在异常规范中使用类型变量是被允许的
```java
public static <T extends Throwable> void dowork(T t) throws T{//OK
    try{
        dowork
    }catch(Throwable realCasue){//catch(T e) 还是错的
        t.initCause(realCasue);
        throw t;
    }
}
```
解释泛型类不能被抛出和捕获:  
1. 泛型类不能被抛出和捕获，而泛型是可以抛出
2. 确切的讲就是我们不能使用泛型类来设计一个自定义异常
3. 如，当我们对于参数为String和参数为Double，要抛出MyException< String>、MyException< Double>两个自定义异常，但是在虚拟机中都是抛出MyExcpetion异常，而捕获这两个异常也是捕获MyExcpetion类，无法做到分别处理，也无法根据情况分别抛出。
4. 但对于T extends Throwable, T类本身不是泛型类，只是对类进行了泛型限定，所以是可以正常抛出的。
5. 而对于catch(T t),T的不确定，导致不同的T应该有不同的方法，显然不应该把T放在这里。

## 可以消除对受检查异常的检查
Java异常处理机制的基本原则是：为所有受检查异常提供一个处理器  
如下代码
```java
public class Block{
    @SupressWarnings("unchecked")
    public static <T extends Throwable> void throwAs(Throwable e) throws T{
        throw (T)e;
    }
}

```
那么通过以下方法,所有传入的异常t，都会被转换成RuntimExcpetion
```java
Block.<RuntimeException>throwAs(t);
```

通过以下实例，可以将一个受查异常转成一个非受查异常
```java
public abstract class Block {
    public abstract void body() throws Exception;
    public Thread toThread(){
        return new Thread(){
            public void run(){
                try{
                    body();
                }catch (Throwable t){
                    Block.<RuntimeException>throwAs(t);
                }
            }
        };
    }

    @SuppressWarnings("unchecked")
    public static <T extends Throwable> void throwAs(Throwable t) throws T{
        throw (T)t;
    }

}


import java.io.File;
import java.util.Scanner;

public class BlockTest {
    public static void main(String[] args) {
        new Block(){
            @Override
            public void body() throws Exception {
                Scanner in = new Scanner(new File("asdsa"), "UTF-8");
            }
        }.toThread().start();
    }
}

```

运行后，因为根本没有"asdsa"这个文件，所以会有一个FileNotFindExcpetion  
正常情况下，需要在run方法种不过这个异常，在包装到非受查异常中。(run方法不抛出任何异常)  
而本例，只是抛出异常（同时消除非受查异常警告），让编译器认为不是受查异常。

## 注意擦除后的冲突
？？？？？？？？？？？？

# 泛型通配符

通配符类型中，允许类型参数变化  
示例：  
如下代码,只能传递Pair< Employee>,而不能传递Pair< Manager>
```java
public static void printBuddies(Pair<Employee>  aPair>){
    ...
}
```
对于这样的限制，就可以使用泛型通配符
```java
public static void printBuddies(Pair<? extend Employee> aPair){
    ...
}
```
这样传入的类型，就是所有的Employee的子类都可以

## 通配符超类限定
如下通配符限制为Managerd的所有超类型
```java
? super Mananger
```