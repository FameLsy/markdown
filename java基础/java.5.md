
title: java 基础:流程控制语句
date: 2018-02-02 00:00:05
tags: 
- 流程控制语句
categories:
- JavaSe教程
---

Java的程序控制语句分为以下几类:选择,重复和跳转

# Java的选择语句
Java支持两种选择语句:if语句和switch语句
## if语句
f语句的完整格式:
```java
if (condition) statement1;
else statement2;
```
## switch语句
switch语句的通用形式
```java
switch (expression) {
    case value1:
    // statement sequence
    break;
    case value2:
    // statement sequence   
    break;
    .
    .
    .
    case valueN:
    // statement sequence
    break;
    default:
    // default statement sequence
}
```
表达式expression必须为byte,short,int或char类型。  
  
每个case语句后的值value必须是与表达式类型兼容的特定的一个常量(它必须为一个常量,而不是变量)。  

重复的case值是不允许的。  

如果没有break语句,程序将继续执行下面的每一个case语句,直到遇到break语句(或switch语句的末尾)。

# 循 环 语 句
Java的循环语句有for, while和 do-while

## while语句
```java
while(condition) {
// body of loop
}
```
因为while语句在循环一开始就计算条件表达式,若开始时条件为假,则循环体一次也
不会执行

## do-while循环
```java
do {
// body of loop
} while (condition);
```
do-while循环总是先执行循环体,然后再计算条件表达式

## for循环
```java
for(initialization; condition; iteration) {
// body
}
```
Java允许你在for循环的初始化部分和反复部分声明多个变量
```java
for(a=1, b=4; a<b; a++, b--) {
    System.out.println("a = " + a);
    System.out.println("b = " + b);
}
```

# 跳 转 语 句
Java 支持 3 种跳转语句:break,continue和return

## break语句
作用：
1. 在switch语句中,它被用来终止一个语句序列
2. 能被用来退出一个循环
3. 能作为一种“先进”的goto 语句来使用

break不是被设计来提供一种正常的循环终止的方法!!!

**把 break 当作 goto 的一种形式来用**
标签break语句的通用格式
```java
label:{
    ...
    break label//直接跳出循环，调到label块的尾部执行some code..
}
// some code..

```
## continue语句
跳出当前循环，开始新的一次循环

## return语句
return语句用来明确地从一个方法返回