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