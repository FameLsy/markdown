---
title: 组合模式
date: 2019 01-01 00:00:01
tags: 
- 组合模式
- 结构型模式
categories: 
- 设计模式
---
# 组合模式

概念：
1. 将对象组合成树形状接口来表示"整体/部分"层次结构，组合能让客户以一致的方式处理这个对象以及对象组合
2. 组合模式能用树形方式创建对象结构，树里包含了组合以及个别的对象

使用场景：
- 部分、整体场景，如树形菜单，文件、文件夹的管理。

优点：
 1. 高层模块调用简单
 2. 节点自由增加。

缺点
- 在使用组合模式时，其叶子和树枝的声明都是实现类，而不是接口，违反了依赖倒置原则。

模式构成:
![composite](https://raw.githubusercontent.com/FameLsy/Images/master/design/composite.png)

代码解释
1. 组合抽象类:为组合中所有对象定义一个接口，不管是组合还是叶节点
2. 具体组合:定义组合 的行为，而且具有子节点
3. 叶节点：没有孩子，虽然继承了add等方法但是没有用

组合抽象类
```java
/**
 * 为组合中所有对象定义一个接口，不管是组合还是叶节点
 * @author liisyu
 * @date 2019/2/23
 */
public abstract class Component {
    /**
     * 操作
     */
    public void operation(){
        throw new UnsupportedOperationException();
    };

    /**
     * 添加组合
     * @param component 添加的组合
     */
    public void add(Component component){
        throw new UnsupportedOperationException();
    };

    /**
     * 删除组合
     * @param component 删除的组合
     */
    public void remove(Component component){
        throw new UnsupportedOperationException();
    };

    /**
     * 获取子节点
     * @return
     */
    public Component getChild(int i){
        throw new UnsupportedOperationException();
    }
}
```

具体组合
```java
/**
 * 定义组合 的行为，而且具有子节点
 * @author liisyu
 * @date 2019/2/23
 */
public class Composite extends Component {
    List<Component> list;
    String description;
    public Composite(String description) {
        list = new ArrayList<>();
        this.description = description;
    }

    @Override
    public void operation() {
        System.out.println(description);
    }

    @Override
    public void add(Component component) {
        list.add(component);
    }

    @Override
    public void remove(Component component) {
        list.remove(component);
    }

    @Override
    public Component getChild(int i) {
        return (Component)list.get(i);
    }
}
```

叶节点
```java
/**
 * 叶节点
 * 没有孩子，虽然继承了add等方法但是没有用
 * @author liisyu
 * @date 2019/2/23
 */
public class Leaf extends Component{
    String description;
    public Leaf(String description) {
        this.description = description;
    }

    @Override
    public void operation() {
        System.out.println(description);
    }

}
```

使用
```java
/**
 * @author liisyu
 * @date 2019/2/23
 */
public class TestMain {
    public static void main(String[] args) {
        Composite composite = new Composite("一号组合");
        Leaf leaf = new Leaf("一号叶节点");
        //放入
        composite.add(leaf);

        Composite composite2 = new Composite("二号组合");
        Leaf leaf2 = new Leaf("二号叶节点");
        //放入
        composite2.add(leaf2);

        //将composite2放入composite
        composite.add(composite2);

        //现在有如下结构
//        Composite
//            |
//      leaf      composite2
//                     |
//                    leaf2

//        验证
        composite.operation();
        composite.getChild(0).operation();

        Component child = composite.getChild(1);
        child.operation();

        child.getChild(0).operation();
    }
}
//print
一号组合
一号叶节点
二号组合
二号叶节点
```


