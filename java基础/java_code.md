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