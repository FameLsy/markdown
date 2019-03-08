---
title: Spring-AOP-具体实现
tgas: 
- Spring-具体实现
categories: 
- Spring 
---

# AOP的具体实现
1. xml实现
2. java注解实现


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