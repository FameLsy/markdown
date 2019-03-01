---
title: 命令模式
date: 2019 01-01 00:00:01
tags: 
- 命令模式
- 行为型模式
categories: 
- 设计模式
---
# 命令模式
概念：  
将一个封装成一个对象，从而可以用不同的请求对客户进行参数化

使用场景：
对行为进行"记录、撤销/重做、事务"等处理，这种无法抵御变化的紧耦合是不合适的。在这种情况下，将一组行为抽象为对象，可以实现二者之间的松耦合。

优点:
1. 降低系统耦合度
2. 新的命令可以很容易添加到系统中去

模式构成:

![command](https://raw.githubusercontent.com/FameLsy/Images/master/design/command.png)

代码解释:
1. 抽象命令类(Command):execute()方法供子类执行接收者的命令，undo()方法撤销命令
2. 命令实体类
3. 接收者类(Receiver): 任何类都可以是接收者类，真正执行逻辑的地方
4. 命令调用者(Invoker):封装了命令，执行excute()方法的地方
5. 客户端类(Client):创建命令，并指定接收者

创建接口
```java
/**
 * 命令接口
 * @author liisyu
 * @date 2019/02/23
 */
public interface Command {
    /**
     * 执行方法
     */
    void execute();

    /**
     * 撤销方法
     */
    void undo();
}
```

接口实现
```java
/**
 * 命令接口
 * （封有Receiver对象，告诉高达该怎么做）
 * @author liisyu
 * @date 2019/2/23
 */
public class GoCommand implements Command {
    private Receiver receiver;

    public GoCommand(Receiver receiver) {
        this.receiver = receiver;
    }

    @Override
    public void execute() {
        receiver.action();
    }

    @Override
    public void undo() {
        receiver.undoAction();
    }
}

```

命令调用者
```java
/**
 * 命令调用者
 * (模拟高达战舰，拥有一系列的命令，告诉高达们怎么作战)
 *
 * @author liisyu
 * @date 2019/2/23
 */
public class Invoker {
    private List<Command> commands;
    private Command noCommand;
    public Invoker(){
        this.commands = new ArrayList<>();
        noCommand = new NoCommand();
    }
    public void setCommand(Command command) {
        commands.add(command);
    }

    public void invokeCommand(){
        for (Command command : commands){
            command.execute();
//           追踪上一条命令
            noCommand = command;
        }

//       如有需要，可以执行完清除命令
        commands.clear();
    }

    public void undoCommand(){
        noCommand.undo();
    }
}
```

命令接收者
```java
/**
 * 命令接收者
 * 知道如何进行必要的工作，实现这个请求
 * 任何类都可以当接收者
 *(模拟成高达，可以出击或是返航)
 * @author liisyu
 * @date 2019/2/23
 */
public class Receiver {
    public Receiver() {
        System.out.println("Freedom准备就绪！");
    }

    public void action(){
        System.out.println("高达，出击！");
    }

    public void undoAction(){
        System.out.println("高达，返航！");
    }
}
```

客户端
```java
/**
 * 客户端
 *（模拟指挥官）
 * @author liisyu
 * @date 2019/2/23
 */
public class Client {
    public static void main(String[] args) {
        System.out.println("模拟战争开始");
//        首先我们需要一价高达(接收者)
        Receiver receiver = new Receiver();
//        需要下达什么命令呢，出击命令
        GoCommand goCommand = new GoCommand(receiver);
//        我们还需要战舰，用来调用命令
        Invoker invoker = new Invoker();
//        将新的命令输入进去
        invoker.setCommand(goCommand);
//        执行
        invoker.invokeCommand();
//        发生紧急情况，需要撤销命令
        System.out.println("情况不对!");
        invoker.undoCommand();
    }
}
//print
模拟战争开始
Freedom准备就绪！
高达，出击！
情况不对!
高达，返航！
```

无命令类,主要用于撤销操作开始的初始化
```
/**
 *无命令
 * @author liisyu
 * @date 2019/2/23
 */
public class NoCommand implements Command {
    @Override
    public void execute() {
        System.out.println("无命令");
    }

    @Override
    public void undo() {
        System.out.println("无命令可撤销");
    }
}
```

以上模拟了一个高达出击的命令模式
1. 指挥官（Client）决定命令(Command)和谁去接受命令(Receiver)
2. 指挥官不可能喊着高达去执行命令，需要通过战舰(invoker)传达去执行命令
3. 如果有新的命令(new Command(receiver ),无需更改，战舰(invoker.setCommand)完全可以接受它
4. 如果有新的集体(new Receiver()),旧的命令也可以接受它(比方说aCommand.setReceiver(receiver))
5. 出现一对多，对多对方式也可以相应的使用集合

