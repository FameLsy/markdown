---
title: 工厂模式
tags: 
- 工厂模式
- 创建型模式
categories: 
- 设计模式
---
# 工厂模式
工厂模式有三个分类
1. 简单工厂模式
2. 工厂方法模式
3. 抽象工厂模式

概念：  
1. 定义了一个创建对象的接口，但由子类决定要实例化的类是哪一个，工厂方法让类把实例化推迟到子类。
2. 

优点:
1. 一个调用者想创建一个对象，只要知道其名称就可以了。 
2. 扩展性高，如果想增加一个产品，只要扩展一个工厂类就可以。 
32. 屏蔽产品的具体实现，调用者只关心产品的接口。

## 简单工厂模式

使用场景：
- 创建的对象较少


模式构成:

![simple](https://raw.githubusercontent.com/FameLsy/Images/master/design/simple.png)

代码解释:（以机器人厂为例）
1. 抽象接口(Robot): 子类定制不同实例
2. 具体实例
3. 工厂：从工厂获取各类实例(可以使用反射进行优化)

创建一个接口
```java
/**
 * 机器人接口
 * @author liisyu
 * @date 2019/02/22
 */
public interface Robot {
    /**
     * 机器人出击方法描述
     */
    void go();
}
```
实例
```java
/**
 * 机器人实现类（扎古）
 * @author liisyu
 * @date 2019/02/22
 */
public class Zhagu implements Robot {
    public Zhagu() {
        System.out.println("扎古，出击！");
    }

    @Override
    public void go() {
        System.out.println("时速500公里/小时");
    }
}

/**
 * 机器人实现类(高达Zero)
 * @author liisyu
 * @date 2019/02/22
 */
public class GaodaZero implements Robot {
    public GaodaZero() {
        System.out.println("高达，出击！");
    }

    @Override
    public void go() {
        System.out.println("时速1500公里/小时，瞬移100米");
    }
}
```

工厂类,这里提供了两种方式进行获取实例
```java
/**
 * 机器人工厂
 * @author liisyu
 * @date 2019/02/22
 */
public class RobotFactory {
    /**
     * 获取机器人
     * @param robotType 机器人类型
     * @return 机器人实例
     */
    public static Robot getRobot(String robotType){
        if (robotType == null || robotType.length() == 0){
            return null;
        }
        if ("zhagu".equalsIgnoreCase(robotType)) {
            return new Zhagu();
        }else if("gaoda".equalsIgnoreCase(robotType)){
            return new GaodaZero();
        }
        return null;
    }

    /**
     * 反射方式获取机器人
     * @param robotType 机器人类型
     * @return  机器人实例
     */
    public static Robot getRobot2(String robotType) {
        Robot robot = null;
        try {
            robot =  (Robot) Class.forName("com.liisyu.factory.simple." + robotType).newInstance();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return robot;
    }
}

```

使用
```java
/**
 * Test Class
 * @author liisyu
 * @date 2019/02/22
 */
public class TestMain {
    public static void main(String[] args) {
        Robot robot = RobotFactory.getRobot("gaoda");
        robot.go();

        robot = RobotFactory.getRobot2("zhagu");
        robot.go();
    }
}

//print
高达，出击！
时速1500公里/小时，瞬移100米
扎古，出击！
时速500公里/小时
```
##  工厂方法模式

使用场景：
1. 一个类不知道它所需要的对象的类
2. 一个类通过其子类来指定创建哪个对象
3. 将创建对象的任务委托给多个工厂子类中的某一个，客户端在使用时可以无需关心是哪一个工厂子类创建产品子类，需要时再动态指定，可将具体工厂类的类名存储在配置文件或数据库中

模式构成:

![mehotd](https://raw.githubusercontent.com/FameLsy/Images/master/design/mehotd.png)

代码解释
1. 抽象工厂类
2. 抽象对象类
3. 工厂类实现，每一个对象对应一个工厂
4. 对象实现类

工厂接口
```java
/**
 * 工厂接口
 * @author liisyu
 * @date 2019/02/22
 */
public interface Factory {
    /**
     * 工厂方法,所有子类必须继承
     */
    Robot factoryMethod();
}

```

机器人接口
```java
/**
 * 机器人接口
 * @author liisyu
 * @date 2019/02/22
 */
public interface Robot {
    /**
     * 机器人出击方法描述
     */
    void go();
}

```

工厂实现类
```java
/**
 * 高达Zero工厂
 * @author liisyu
 * @date 2019/02/22
 */
public class GaodaFactory implements Factory{

    @Override
    public Robot factoryMethod() {
        return new GaodaZero();
    }
}


/**
 * 扎古工厂
 * @author liisyu
 * @date 2019/02/22
 */
public class ZhaguFactory implements Factory {

    @Override
    public Robot factoryMethod() {
        return new Zhagu();
    }
}

```

对象实例
```java
/**
 * 机器人实现类（扎古）
 * @author liisyu
 * @date 2019/02/22
 */
public class Zhagu implements Robot {
    public Zhagu() {
        System.out.println("扎古，出击！");
    }

    @Override
    public void go() {
        System.out.println("时速500公里/小时");
    }
}

/**
 * 机器人实现类(高达Zero)
 * @author liisyu
 * @date 2019/02/22
 */
public class GaodaZero implements Robot {
    public GaodaZero() {
        System.out.println("高达，出击！");
    }

    @Override
    public void go() {
        System.out.println("时速1500公里/小时，瞬移100米");
    }
}
```

使用,实例化不同工厂，获取相应的对象
```java
/**
 * Test Class
 * @author liisyu
 * @date 2019/02/22
 */
public class TestMain {
    public static void main(String[] args) {
        Robot robot = new GaodaFactory().factoryMethod();
        robot.go();

        robot = new ZhaguFactory().factoryMethod();
        robot.go();
    }
}
//print
高达，出击！
时速1500公里/小时，瞬移100米
扎古，出击！
时速500公里/小时
```

## 抽象工厂模式

使用场景：
- 工厂不止创建一个对象

模式构成:

![abstract](https://raw.githubusercontent.com/FameLsy/Images/master/design/abstract.png)

代码解释:
1. 抽象工厂类（复数）
2. 抽象对象类（复数）
3. 工厂类实现，每一个对象对应一个工厂
4. 对象实现类

工厂接口,包含了两个方法，获取机器人和武器
```java
/**
 * 工厂接口
 * @author liisyu
 * @date 2019/02/22
 */
public interface Factory {
    /**
     * 工厂方法,所有子类必须继承
     * @return 机器人实体类
     */
    Robot factoryMethod();

    /**
     * 工厂方法,所有子类必须继承
     * @return 武器实体类
     */
    Arms factoryMethod2();
}
```

机器人接口
```java
/**
 * 机器人接口
 * @author liisyu
 * @date 2019/02/22
 */
public interface Robot {
    /**
     * 机器人出击方法描述
     */
    void go();
}

```
工厂实现类,高达工厂产高达和炸弹，扎古工厂产扎古和枪
```java
/**
 * 高达Zero工厂
 * @author liisyu
 * @date 2019/02/22
 */
public class GaodaFactory implements Factory {

    @Override
    public Robot factoryMethod() {
        return new GaodaZero();
    }

    @Override
    public Arms factoryMethod2() {
        return new Bomb();
    }
}


/**
 * 扎古工厂
 * @author liisyu
 * @date 2019/02/22
 */
public class ZhaguFactory implements Factory {

    @Override
    public Robot factoryMethod() {
        return new Zhagu();
    }

    @Override
    public Arms factoryMethod2() {
        return new Guns();
    }
}

```

对象实例
```java
/**
 * 机器人实现类（扎古）
 * @author liisyu
 * @date 2019/02/22
 */
public class Zhagu implements Robot {
    public Zhagu() {
        System.out.println("扎古，出击！");
    }

    @Override
    public void go() {
        System.out.println("时速500公里/小时");
    }
}

/**
 * 机器人实现类(高达Zero)
 * @author liisyu
 * @date 2019/02/22
 */
public class GaodaZero implements Robot {
    public GaodaZero() {
        System.out.println("高达，出击！");
    }

    @Override
    public void go() {
        System.out.println("时速1500公里/小时，瞬移100米");
    }
}
```

使用
```java
/**
 * Test Class
 * @author liisyu
 * @date 2019/02/22
 */
public class TestMain {
    public static void main(String[] args) {
        Factory factory = new GaodaFactory();
        Robot robot = factory.factoryMethod();
        Arms arms = factory.factoryMethod2();
        robot.go();
        arms.run();

        factory = new ZhaguFactory();
        robot = factory.factoryMethod();
        arms = factory.factoryMethod2();
        robot.go();
        arms.run();
    }
}
//print
高达，出击！
时速1500公里/小时，瞬移100米
爆炸输出
扎古，出击！
时速500公里/小时
单点高伤害
```
