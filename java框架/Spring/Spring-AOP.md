---
title: Spring-AOP
date: 2018-02-27 00:00:02
tgas: 
- Spring-AOP
categories: 
- Spring 
---

知识点：
1. AOP概念
2. AOP相关术语解释
3. AOP通知
4. AOP切点表达式
4. AOP的具体实现

# 什么是AOP?  
面向侧面的程序设计（aspect-oriented programming，AOP，又译作面向方面的程序设计、观点导向编程、剖面导向程序设计）是计算机科学中的一个术语，指一种程序设计范型。该范型以一种称为侧面（aspect，又译作方面）的语言构造为基础，侧面是一种新的模块化机制，用来描述分散在对象、类或函数中的横切关注点 -- wiki

作用
1. AOP采取横向抽取机制，补充了传统纵向继承体系（OOP）无法解决的重复性代码优化（性能监视、事务管理、安全检查、缓存）
2. 将业务逻辑和系统处理的代码（关闭连接、事务管理、操作日志记录）解耦。

横向的抽取机制

![横向](https://raw.githubusercontent.com/FameLsy/Images/master/spring/横向.png)

相对的，我们平常使用的是纵向抽取机制

![纵向](https://raw.githubusercontent.com/FameLsy/Images/master/spring/纵向.png)

# aop术语

1. Joinpoint(连接点)   -- 所谓连接点是指那些被拦截到的点。在spring中,这些点指的是方法,因为spring只支持方法类型的连接点
2. Pointcut(切入点)        -- 所谓切入点是指我们要对哪些Joinpoint进行拦截的定义
3. Advice(通知/增强)    -- 所谓通知是指拦截到Joinpoint之后所要做的事情就是通知.通知分为前置通知,后置通知,异常通知,最终通知,环绕通知(切面要完成的功能)
4. Introduction(引介) -- 引介是一种特殊的通知在不修改类代码的前提下, Introduction可以在运行期为类动态地添加一些方法或Field
5. Target(目标对象)     -- 代理的目标对象
6. Weaving(织入)      -- 是指把增强应用到目标对象来创建新的代理对象的过程
7. Proxy（代理）       -- 一个类被AOP织入增强后，就产生一个结果代理类
8. Aspect(切面)        -- 是切入点和通知的结合，以后咱们自己来编写和配置的
9. Advisor（通知器、顾问）		--和Aspect很相似

# 实现原理分析

# AOP-MAVEN

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aspects</artifactId>
    <version>5.0.7.RELEASE</version>
</dependency>
<dependency>
    <groupId>aopalliance</groupId>
    <artifactId>aopalliance</artifactId>
    <version>1.0</version>
</dependency>
```

# 实现
## 切入点表达式
格式：
```xml
execution([修饰符] 返回类型 包名.类名.方法名(参数)
```
1. execution:必须
2. 修饰符：可省略
3. 返回类型：必须，可用*代替
4. 包名：  
    多级包之间用 . 分割  
    包名可用*代替，多级包使用多个*代替  
    生路中间包名可以用..(如*..*.UserDaoImpl.insert())
5. 类名:  
    可用*代替  
    也可以用*通配(如,*DaoImpl)  
6. 方法名：  
    可以使用*代替  
    可以使用*通配(如,add*)  
7. 参数：  
    可以使用*代替  
    多个参数，可以使用..代替  

## 通知类型

通知类型有五种
1. 前置通知
2. 后置通知
3. 最终通知
4. 环绕通知
5. 异常抛出通知

前置通知：
1. 执行在目标对象方法之前
2. 使用< aop:before >标签
3. 应用于方法开始时校验

后置通知：
1. 执行在目标方法之后，有异常不执行
2. 使用< aop:after-returning >
3. 应用于修改方法的返回值

最终通知：
1. 执行在目标方法完成之后，在后置通知之后，有无异常都会执行
2. 使用< aop:after >标签
3. 应用于如释放资源

异常抛出通知：
1. 执行于异常抛出后
2. 使用< aop:after-throwing >标签
3. 应用于包装异常

环绕通知:
1. 相当于前四个通知的合体
2. 使用< aop:around >标签
3. 应用于事务、统计代码执行时机等

## XML方式实现

类图
1. 通知类(MyAdvice):定义了处理各种通知的方法，对于环绕通知需要传入ProceedingJoinPoint类
2. 接口: 目标对象接口
3. 接口实现：目标对象
4. 还需要一个xml配置文件

![aop](https://raw.githubusercontent.com/FameLsy/Images/master/spring/aop.png)


通知类
```
/**
 * @author liisyu
 * @date 2019/2/23
 */
public class MyAdvice {
    //演示前置通知
    public void log() {
        System.out.println("前置...");
    }

    //演示后置通知
    public void log2() {
        System.out.println("后置...");
    }
    //演示最终通知
    public void log3() {
        System.out.println("最终...");
    }
    //演示异常抛出通知
    public void log4() {
        System.out.println("异常...");
    }

    /**
     * 环绕通知
     * @param joinPoint 需要传入的方法
     */
    public void log5(ProceedingJoinPoint joinPoint)  {
        System.out.println("前置通知");
        //调用目标对象的方法
        try {
            joinPoint.proceed();
            System.out.println("后置通知");
        } catch (Throwable e) {
            //相当于实现异常通知
            System.out.println("异常抛出配置");
            e.printStackTrace();
        }finally {
            System.out.println("最终通知");
        }
    }
}
```

目标对象接口
```java
/**
 * @author liisyu
 * @date 2019/2/28
 */
public interface UserDao {
    void insert();
}
```

目标对象实例
```java
/**
 * @author liisyu
 * @date 2019/2/28
 */
public class UserDaoImpl implements UserDao {
    @Override
    public void insert() {

        System.out.println("insert()");
//        用于手动创造异常
//        int i = 1/0;
    }
}
```
配置文件
```xml
<bean class="com.liisyu.demo1.UserDaoImpl"/>
<!--配置通知、增强-->
<bean id="myAdvice" class="com.liisyu.demo1.MyAdvice"/>

<!--AOP配置-->
<aop:config>
    <aop:aspect ref="myAdvice">
        <aop:pointcut id="pointcut"
                    expression="execution(void com.liisyu.demo1.UserDaoImpl.insert())"/>
    <!--<aop:before method="log"-->
                <!--pointcut="execution(void com.liisyu.dao.impl.UserDaoImpl.insert())"/>-->
    <!--<aop:after-returning method="log2"-->
                <!--pointcut="execution(void com.liisyu.dao.impl.UserDaoImpl.insert())"/>-->
    <!--<aop:after method="log3"-->
                <!--pointcut="execution(void com.liisyu.dao.impl.UserDaoImpl.insert())"/>-->
    <!--<aop:after-throwing method="log4"-->
                        <!--pointcut="execution(void com.liisyu.dao.impl.UserDaoImpl.insert())"/>-->
    <!--<aop:around method="log5"-->
                <!--pointcut="execution(void com.liisyu.demo1.UserDaoImpl.insert())"/>-->

    <aop:around method="log5"
                pointcut-ref="pointcut"/>
    </aop:aspect>
</aop:config>
</beans>
```
此外，还可以通过< aop-pointcut >标签来设置切入点
```xml
<aop:pointcut id="pointcut"  expression="execution(void com.liisyu.demo1.UserDaoImpl.insert())"/>

<aop:around method="log5" pointcut-ref="pointcut"/>
```
## 注解方式实现

类图
1. 切面类:包含通知和切入点
2. 接口: 目标对象接口
3. 接口实现：目标对象
4. 配置类：需要实现@EnableAspectJAutoProxy自动代理

![aop2](https://raw.githubusercontent.com/FameLsy/Images/master/spring/aop2.png)

切面类
```java
/**
 * 切面类
 * @author liisyu
 * @date 2019/2/28
 */
@Component("myAspect")
@Aspect
public class MyAspect {
    //演示前置通知
    @Before(value = "execution(void com.liisyu.demo2.UserDaoImpl.insert())")
    public void log() {
        System.out.println("前置...");
    }

    @AfterReturning(value = "execution(void com.liisyu.demo2.UserDaoImpl.insert())")
    //演示后置通知
    public void log2() {
        System.out.println("后置...");
    }

    @After(value = "execution(void com.liisyu.demo2.UserDaoImpl.insert())")
    //演示最终通知
    public void log3() {
        System.out.println("最终...");
    }

    @AfterThrowing(value = "execution(void com.liisyu.demo2.UserDaoImpl.insert())")
    //演示异常抛出通知
    public void log4() {
        System.out.println("异常...");
    }

    @Around(value = "execution(void com.liisyu.demo2.UserDaoImpl.insert())")
    public void log5(ProceedingJoinPoint joinPoint)  {
        System.out.println("前置通知");
        //调用目标对象的方法
        try {
            joinPoint.proceed();
            System.out.println("后置通知");
        } catch (Throwable e) {
            //相当于实现异常通知
            System.out.println("异常抛出配置");
            e.printStackTrace();
        }finally {
            System.out.println("最终通知");
        }
    }
}
```

目标对象接口
```java
/**
 * @author liisyu
 * @date 2019/2/28
 */
public interface UserDao {
    void insert();
}
```

目标对象实例
```java
/**
 * @author liisyu
 * @date 2019/2/28
 */
public class UserDaoImpl implements UserDao {
    @Override
    public void insert() {

        System.out.println("insert()");
//        用于手动创造异常
//        int i = 1/0;
    }
}
```

配置类
```java
/**
 * 配置类
 * @author liisyu
 * @date 2019/2/28
 */
@Configuration
@ComponentScan(basePackages = "com.liisyu.demo2")
@EnableAspectJAutoProxy
public class SpringConfiguration {

}
```

此外，还可以使用@Pointcut提取切入点
```java
    @Pointcut(value = "execution(void com.liisyu.demo2.UserDaoImpl.insert())")
    public void pointcut(){}

    //演示前置通知
    @Before(value = "pointcut()")
    public void log() {
        System.out.println("前置...");
    }
```