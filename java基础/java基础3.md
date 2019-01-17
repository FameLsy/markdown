---
title: java 基础(3):继承
tags: 
- java基础
categories:
- JavaSe教程
---


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
```
7. 不要过多地使用反射
```