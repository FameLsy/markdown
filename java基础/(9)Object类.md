---
title: java 基础(9):Object类
date: 2018-02-02 00:00:09
tags: 
- Object类
categories:
- JavaSe教程
---

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