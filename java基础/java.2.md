---
title: java 基础:变量
date: 2018-02-02 00:00:02
tags: 
- 变量
categories:
- JavaSe教程
---

Java语言中3个最基本的元素:数据类型,变量和数组

# 变量

##　声明变量
基本的变量声明方法如下
```java
// type是Java的基本类型之一,或类及接口类型的名字  
//标识符(identifier)是变量的名字,指定一个等号和一个值来初始化变量。
type identifier [ = value][,identifier [= value] ...] ;
```

## 变量命名  

```
字母[a~zA~Z_$]
```

需要注意的是 **$** 虽然合法，但一般不使用在自己的代码中，只用于Java编译器或其他工具生成的名字中;对于讲变量名命名为类型名，许多程序员喜欢加上前缀"a",如Box abox

## 变量的作用域和生存期
程序块被包括在一对大括号中。一个程序块定义了一个作用域(scope)  
变量在其作用域内被创建,离开其作用域时被撤消  

## 常量
利用关键字final指示常量，只能赋值一次,且常量名习惯上使用全大写加 *_*的方式命名  

### 类常量
通过关键字 *static final* 设置的常量
