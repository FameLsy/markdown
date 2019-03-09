---
title: 装饰者模式
date: 2019 01-01 00:00:01
tags: 
- 装饰者模式
- 结构型模式
categories: 
- 设计模式
---
# 装饰者模式
概念:
- 允许向一个现有的对象添加新的功能，同时又不改变其结构。这种类型的设计模式属于结构型模式，它是作为现有的类的一个包装。

使用场景：  
1. 扩展一个类的功能
2. 动态增加功能
3. Java的I/O流

优点:  
- 装饰类和被装饰类可以独立发展，不会相互耦合，装饰模式是继承的一个替代模式，装饰模式可以动态扩展一个实现类的功能。

模式构成:  

![装饰者模式](https://raw.githubusercontent.com/FameLsy/Images/master/design/Decorator.png)

代码解释:
1. 需要一个顶级的组件类或接口
2. 被装饰者和装饰类都需要继承/实现它
3. 装饰者需要注入被装饰者的类，以便在原有基础上添加新功能

创建组件抽象类(也可以是接口),我们将对它的两个方法进行装饰
```java
/**
 * 组件抽象类
 * @author liisyu
 * @date 2019/02/22
 */
public abstract class BaseComponent {
    String description = "Unknown component";
    /**
     * 组件的方法A
     */
    public String getDescription(){
        return description;
    }

    /**
     * 获取功能
     * @return 功能描述
     */
   public abstract String features();
}
```
组件子类,把被装饰者当成了机器人，那么装饰器就是为它装备各种武器
```java
/**
 * 头部组件(就当拼装机器人了)
 * @author liisyu
 * @date 2019/02/22
 */
public class RobotComponent extends BaseComponent{
    public RobotComponent() {
        description = "我是一个机器人";
    }

    @Override
    public String features() {
        return "战争武器";
    }
}
```
创建基本的装饰器,它通用扩展于BaseComponent
```java
/**
 * 装饰器
 * @author liisyu
 * @date 2019/02/22
 */
public abstract class BaseDecorator extends BaseComponent{
    /**
     * 这里重新抽象化了getDescription,目的就是让其子类不得不重写它
     * @return 返回描述
     */
    @Override
    public abstract String getDescription();
}
```
装饰器子类,这里定义了装备枪和炸弹的装饰器
```java
/**
 * 装饰器实体类
 * @author liisyu
 * @date 2019/02/22
 */
public class GunDecorator extends BaseDecorator {
    /**
     * 用一个变量引用被装饰柱
     */
    private BaseComponent baseComponent;

    /**
     * 将被装饰者传入到装饰器中
     * @param baseComponent
     */
    public GunDecorator(BaseComponent baseComponent) {
        this.baseComponent = baseComponent;
    }

    @Override
    public String getDescription() {
        return baseComponent.getDescription() + ",装备枪";
    }

    @Override
    public String features() {
        return baseComponent.features() + ",大范围杀伤力,战斗力+5";
    }
}

/**
 * 装饰器实体类
 * @author liisyu
 * @date 2019/02/22
 */
public class BombDecorator extends BaseDecorator {
    private BaseComponent baseComponent;

    public BombDecorator(BaseComponent baseComponent) {
        this.baseComponent = baseComponent;
    }

    @Override
    public String getDescription() {
        return baseComponent.getDescription() + ",装备炸弹";
    }

    @Override
    public String features() {
        return baseComponent.features() + ",攻击范围+500px";
    }
}

```

使用,先创建了一个机器人，然后分别装上枪和炸弹
```java
/**
 * Test Class
 * @author liisyu
 * @date 2019/02/22
 */
public class TestMain {
    public static void main(String[] args) {
        //创建一个机器人组件
        BaseComponent baseComponent = new RobotComponent();
        System.out.println(baseComponent.getDescription() + "&&" + baseComponent.features());

        //装备枪
        baseComponent = new GunDecorator(baseComponent);
        System.out.println(baseComponent.getDescription() + "&&" + baseComponent.features());

        //装备炸弹
        baseComponent = new BombDecorator(baseComponent);
        System.out.println(baseComponent.getDescription() + "&&" + baseComponent.features());
    }
}

//print
我是一个机器人&&战争武器
我是一个机器人,装备枪&&战争武器,大范围杀伤力,战斗力+5
我是一个机器人,装备枪,装备炸弹&&战争武器,大范围杀伤力,战斗力+5,攻击范围+500px

```


