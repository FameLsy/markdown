---
title: 外观模式
date: 2019 01-01 00:00:01
tags: 
- 外观模式
- 结构型模式
categories: 
- 设计模式
---
# 外观模式
概念：  
- 提供了一个统一的接口，用来访问子系统中的一群接口。外观定义了一个高层接口，让子系统更容易使用

使用场景：
- 为复杂的模块或子系统提供外界访问的模块

优点:
1. 减少系统相互依赖。 
2. 提高灵活性
3. 提高了安全性。

模式构成:
![facede](https://raw.githubusercontent.com/FameLsy/Images/master/design/facede.png)

代码解释:
1. 各个子系统类
2. 外观类类(Facede):核心，所有子系统方法在这里封装成一个方法
3. 客户端：只需要调用外观类类，无需关系其他子系统

子系统类(以高达为例，它内部有传感器、核心电脑、引擎等系统,每个都有一个start()方法)
```
/**
 * 子系统类
 * (高达核心电脑)
 * @author liisyu
 * @date 2019/2/23
 */
public class CoreComputer {
    public void start(){
        System.out.println("核心电脑，启动");
    }
}


/**
 * 子系统
 * (高达引擎)
 * @author liisyu
 * @date 2019/2/23
 */
public class Engine {
    public void start(){
        System.out.println("高达引擎，启动");
    }
}

/**
 * 子系统
 * (高达传感器)
 * @author liisyu
 * @date 2019/2/23
 */
public class Sensor {
    public void start(){
        System.out.println("高达传感器，启动");
    }
}
```

外观类，我们将所有子系统的start()方法集中Facede的start()方法中
```java
/**
 * 外观类
 * (一架高达)
 * @author liisyu
 * @date 2019/2/23
 */
public class Facede {
    private CoreComputer coreComputer;
    private Engine engine;
    private Sensor sensor;

    public Facede() {
        this.coreComputer = new CoreComputer();
        this.engine = new Engine();
        this.sensor = new Sensor();
    }

    /**
     * 高达启动！
     */
    public void start(){
        coreComputer.start();
        engine.start();
        sensor.start();
        System.out.println("高达启动完毕");
    };
}
```

客户端,直接创一个外观类，并调用start()方法，就可以开启全部的子系统
```java
/**
 * 客户端
 * @author liisyu
 * @date 2019/2/23
 */
public class Client {
    public static void main(String[] args) {
//        创建外观类
        Facede facede = new Facede();
//        启动,并不需要其他引擎系统，传感器系统，核心系统是怎么启动的
        facede.start();

    }
}
//print
核心电脑，启动
高达引擎，启动
高达传感器，启动
高达启动完毕
```
