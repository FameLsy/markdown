---
title: 策略模式
date: 2019 01-01 00:00:01
tags: 
- 策略模式
- 行为型模式
categories: 
- 设计模式
---

# 策略模式
概念：  
1. 定义了算法族,分别封装起来,让它们之间可以互相替换,此模式让算法的变化独立于使用算法的客户（
2. 通俗的将就是将变化的部分和不变化的部分独立开

使用场景：
 1. 如果在一个系统里面有许多类，它们之间的区别仅在于它们的行为，那么使用策略模式可以动态地让一个对象在许多行为中选择一种行为。 
 2. 一个系统需要动态地在几种算法中选择一种。 
 3. 如果一个对象有很多的行为，如果不用恰当的模式，这些行为就只好使用多重的条件选择语句来实现。(在有多种算法相似的情况下，使用 if...else 所带来的复杂和难以维护)

优点:
1. 算法可以自由切换
2. 避免使用多重条件判断
3. 扩展性良好。

模式构成:
1. 需要一个Strategy接口来封装策略方法，供实现类以不同算法实现
2. Context类使用该策略，通过注入不同的策略实现类，来完成一个方法不同的算法

![策略模式](https://raw.githubusercontent.com/FameLsy/Images/master/design/Strategy.png)

代码解释:
1. 抽象策略(Strategy)：将通用的方法封装起来
2. 具体策略(StrategyImpl*):对方法实现不同的算法
3. 策略使用类(Context): 通过注入不同的具体策略，来实现不同的算法

创建一个策略接口，定义一个通用的策略方法
```java
package com.liisyu.strategypattern;

/**
 * 策略模式
 * @author liisyu
 */
public interface Strategy {
    /**
     * some method to invoke
     * @param objects param
     * @return Object
     */
    Object doMethod(Object[] objects);
}
```

创建策略接口实体类,根据不同需求实现不同的策略方法
```java
/**
 * StrategyImpl 1
 * @author liisyu
 */
public class StrategyImplAdd  implements Strategy{
    @Override
    public Object doMethod(Object[] objects) {
        //do for something with StrategyImpl_1.doMethod;
        return null;
    }
}

/**
 * StrategyImpl 12
 * @author liisyu
 */
public class StrategyImplDelete implements Strategy{
    @Override
    public Object doMethod(Object[] objects) {
        //do for something with StrategyImplDelete.doMethod;
        return null;
    }
}

/**
 * StrategyImpl 3
 * @author liisyu
 */
public class StrategyImplUpdate implements Strategy{
    @Override
    public Object doMethod(Object[] objects) {
        //do for something with StrategyImplUpdate.doMethod;
        return null;
    }
}
```

创建使用策略的类
```java
/**
 * a class that uses strategy
 * @author liisyu
 */
public class Context {
    private Strategy strategy;

    public Context(Strategy strategy){
        this.strategy = strategy;
    }

    public Object executeStrategy(Object[] objects){
        return strategy.doMethod(objects);
    }

}
```

使用,通过传入不同的策略实现类，实现不同的需求
```java
/**
 * Test Class
 * @author liisyu
 */
public class TestMain {
    public static void main(String[] args) {
        Context context = new Context(new StrategyImplAdd());
        context.executeStrategy(null);

        context = new Context(new StrategyImplDelete());
        context.executeStrategy(null);

        context = new Context(new StrategyImplUpdate());
        context.executeStrategy(null);


    }
}
```
