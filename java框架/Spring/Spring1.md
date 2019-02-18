---
title: Spring(1)：装配bean
date: 2018-02-03 00:00:01
tgas: 
- java基础
categories: 
- Spring 教程
---






# Spring的配置文件

1. xml配置文件
2. java方式实现的配置类,@Configuration 注解：表明该类为配置类

```java
@Configuration
public class JavaConfig {}
```











# bean中的自动装配bean
```java
package com.spring.ambiguity.quaifier;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

@Component
public class CDPlayer implements MediaPlayer {
    private CompactDisc compactDisc;

    @Autowired
    public CDPlayer(CompactDisc compactDisc) {
        this.compactDisc = compactDisc;
    }

    public void play() {
        System.out.println("CDPlayer");
        compactDisc.sing();
    }
}
```
CDPlayer中自动装配CompactDisc再这里是存在歧义。怎么解决，第一个想到的就是@Qualifier方法来指定bean，但是！显示@Qualifier不能用在构造器方法上。
 ![在这里插入图片描述](https://img-blog.csdn.net/20180912140541608?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01BU09STA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)



所以，这个方法不行，那么，通过setter方法注入呢

先添加了一个setter方法，并使用@Qualifier标注注入bean id为anotherSgtPeppers的bean，同时注释了构造方法上的@Autowired
```java
package com.spring.ambiguity.quaifier;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

@Component
public class CDPlayer implements MediaPlayer {
    private CompactDisc compactDisc;

    @Autowired
    @Qualifier("anotherSgtPeppers")
    public void setCompactDisc(CompactDisc compactDisc) {
        System.out.println("进入了setter方法");
        this.compactDisc = compactDisc;
    }

//    @Autowired
    public CDPlayer(CompactDisc compactDisc) {
        this.compactDisc = compactDisc;
    }
    

    public void play() {
        System.out.println("CDPlayer");
        compactDisc.sing();
    }
}
```

测试下，结果报错，错误的原因是错误的创建CDPlayer bean，NoUniqueBeanDefinitionException: No qualifying bean of 
type 'com.spring.ambiguity.quaifier.CompactDisc'：没有限定CompactDisc。
```
Caused by: org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating
 bean with name 'CDPlayer' defined in file [D:\更改的路径\Desktop\临时
\14\SpringAction\target\classes\com\spring\ambiguity\quaifier\CDPlayer.class]: Unsatisfied 
dependency expressed through constructor parameter 0; nested exception is 
org.springframework.beans.factory.NoUniqueBeanDefinitionException: No qualifying bean of 
type 'com.spring.ambiguity.quaifier.CompactDisc' available: expected single matching bean 
but found 3: anotherSgtPeppers,otherSgtPeppers,sgtPeppers
```
接下来试着把构造器给注释掉，然后添加一个无参的构造器
```java
package com.spring.ambiguity.quaifier;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

@Component
public class CDPlayer implements MediaPlayer {
    private CompactDisc compactDisc;

    @Autowired
    @Qualifier("anotherSgtPeppers")
    public void setCompactDisc(CompactDisc compactDisc) {
        System.out.println("进入了setter方法");
        this.compactDisc = compactDisc;
    }

    public CDPlayer() {
        System.out.println("进入了构造器");
    }

    //    @Autowired
//    public CDPlayer(CompactDisc compactDisc) {
//        this.compactDisc = compactDisc;
//    }


    public void play() {
        System.out.println("CDPlayer");
        compactDisc.sing();
    }
}
```
测试结果
```
进入了构造器
进入了setter方法
CDPlayer
AnotherSgtPeppers的sing方法
```
结果发现，就算没有@Autowire，也会进入构造器【创建bean实例当然需要构造器。。。】
这下就解释了，一开始的有参构造器，因为CompactDIsc有3个bean可以注入，所以测试结果会出错

所以，第一种办法就是，不要提供有参构造器，然后再setter里面通过@Autowrie和@Qualifier来限定bean

那么，就想用构造器怎么办，试试看自定义注释
```java
package com.spring.ambiguity.quaifier;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

@Component
public class CDPlayer implements MediaPlayer {
    private CompactDisc compactDisc;

//    @Autowired
//    @Qualifier("anotherSgtPeppers")
//    public void setCompactDisc(CompactDisc compactDisc) {
//        System.out.println("进入了setter方法");
//        this.compactDisc = compactDisc;
//    }

//    public CDPlayer() {
//        System.out.println("进入了构造器");
//    }

    @Okss
    public CDPlayer(CompactDisc compactDisc) {
        System.out.println("进入了有参构造器");
        this.compactDisc = compactDisc;
    }


    public void play() {
        System.out.println("CDPlayer");
        compactDisc.sing();
    }
}
```
测试：结果可行！
```
进入了有参构造器
CDPlayer
AnotherSgtPeppers的sing方法
```
得出的结论就是

（1）任何bean的产生，都需要一个构造参数
（2）一个bean作为另一个bean的参数，即使不用@Autowire，也会自动注入
（3）如果想要再方法里注入bean，那么就必须要@Autowire了






