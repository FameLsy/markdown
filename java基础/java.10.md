---
title: java 基础:反射
date: 2018-02-02 00:00:10
tags: 
- 反射
categories:
- JavaSe教程
---
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

# 通过反射打印类的信息

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

# 利用反射编写通用toString，打印对象的域值
ObjectAnalyzer类:
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
测试类:
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