---
title: 观察者模式
date: 2019 01-01 00:00:01
tags: 
- 观察者模式
- 行为型模式
categories: 
- 设计模式
---
# 观察者模式

概念： 
-. 定义了对象之间的一对多依赖,这样一来,当一个对象改变状态时,它的所有依赖者都会收到通知并自动更新

使用场景：
1. 一个抽象模型有两个方面，其中一个方面依赖于另一个方面。将这些方面封装在独立的对象中使它们可以各自独立地改变和复用。
2. 一个对象的改变将导致其他一个或多个对象也发生改变，而不知道具体有多少对象将发生改变，可以降低对象之间的耦合度。
3. 一个对象必须通知其他对象，而并不知道这些对象是谁。
4. 需要在系统中创建一个触发链，A对象的行为将影响B对象，B对象的行为将影响C对象……，可以使用观察者模式创建一种链式触发机制。

优点:
1. 观察者和主题是抽象耦合的
2. 改变观察者或主题，并不会影响另一方

模式构成：

![observer](https://raw.githubusercontent.com/FameLsy/Images/master/design/observer.png)

代码解释:
1. 主题接口和观察者实为多对多，或一对多，多对一的关系，这里使用了Subject接口和Observer接口
2. 主题实现类包含了观察者，方便注册，删除，通知观察者
3. 观察者包含了主题接口，注册了该接口即可收到主题的通知 

主题接口，包含注册、删除，通知观察者的三个方法

```java
/**
 * 主题类
 * @author  liisyu
 * @date 2019/02/22
 */
public interface Subject{
    /**
     * 注册观察者
     * @param observer 观察者
     */
    void registerObserver(Observer observer);

    /**
     * 删除观察者
     * @param observer 观察者
     */
    void removeObserver(Observer observer);

    /**
     * 提示所有观察者
     */
    void notifyObservers();
}
```

观察者接口,主要包含一个更新信息的方法
```java
/**
 * 观察者接口
 * @author liisyu
 * @date 2019/02/22
 */
public interface Observer {
    /**
     * 用于观察者更新
     * @param  info 参数
     */
    void update(String info);

    //观察者的其他方法
}

```

主题实现类，除了属性外，还内置了一个观察者集合，用于保存注册的观察者
```java
/**
 * @author  liisyu
 * @date 2019/02/22
 */
public class SubjectImpl implements Subject {
    private List<Observer> observers;

    private String info;

    public SubjectImpl() {
        observers = new ArrayList<>();
    }

    @Override
    public void registerObserver(Observer observer) {
        observers.add(observer);
    }

    @Override
    public void removeObserver(Observer observer) {
        int i = observers.indexOf(observer);
        if (i >= 0) {
            observers.remove(i);
        }

    }

    @Override
    public void notifyObservers() {
        for (Observer observer : observers){
            observer.update(info);
        }
    }

    /**
     * 信息改变时，提醒所有观察者
     */
    public void infoChange(){
        notifyObservers();
    }

    /**
     * 修改信息，并触发改变
     * @param info 新的状态
     */
    public void setStatus(String info) {
        this.info = info;
        System.out.println("信息改变成: " + this.info);
        infoChange();
    }

    //其他方法
}
```
观察者实现类，内置了一个主题接口，用于注册主题(如果是多个主题，也需要用到集合)
```java
/**
 * 观察者实现类
 * @author liisyu
 * @date 2019/02/22
 */
public class ObserverImpl implements Observer {
    private String info;
    private Subject subject;

    public ObserverImpl(Subject subject) {
        this.subject = subject;
        subject.registerObserver(this);
    }

    @Override
    public void update(String info) {
        this.info = info;
        System.out.println("观察者更新了");
    }

    public Subject getSubject() {
        return subject;
    }

    public void setSubject(Subject subject) {
        this.subject = subject;
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
        SubjectImpl subject = new SubjectImpl();

        ObserverImpl observer = new ObserverImpl(subject);

        subject.setStatus("更新");
    }
}
```
