# Spring-IOC

## 使用Spring框架的好处是什么？
- 轻量：Spring 是轻量的，基本的版本大约2MB。
- 控制反转：Spring通过控制反转实现了松散耦合，对象们给出它们的依赖，而不是创建或查找依赖的对象们。
- 面向切面的编程(AOP)：Spring支持面向切面的编程，并且把应用业务逻辑和系统服务分开。
- 容器：Spring 包含并管理应用中对象的生命周期和配置。
- MVC框架：Spring的WEB框架是个精心设计的框架，是Web框架的一个很好的替代品。
- 事务管理：Spring 提供一个持续的事务管理接口，可以扩展到上至本地事务下至全局事务（JTA）。
- 异常处理：Spring 提供方便的API把具体技术相关的异常（比如由JDBC，Hibernate or JDO抛出的）转化为一致的unchecked 异常。

## Spring七大模块组成?
- Spring Core
- Spring Context
- Spring DAO
- Spring ORM
- Spring AOP
- Spring Web
- Spring Web MVC

## spring 中beanFactory和ApplicationContext的区别
BeanFacotry是spring中比较原始的Factory。如XMLBeanFactory就是一种典型的BeanFactory。原始的BeanFactory无法支持spring的许多插件，如AOP功能、Web应用等。  

ApplicationContext接口,它由BeanFactory接口派生而来，因而提供BeanFactory所有的功能。ApplicationContext以一种更向面向框架的方式工作以及对上下文进行分层和实现继承，ApplicationContext包还提供了以下的功能：  
1.  MessageSource, 提供国际化的消息访问  
2. 资源访问，如URL和文件  
3. 事件传播  
4. 载入多个（有继承关系）上下文 ，使得每一个上下文都专注于一个特定的层次，比如应用的web层  


1.利用MessageSource进行国际化  
   BeanFactory是不支持国际化功能的，因为BeanFactory没有扩展Spring中MessageResource接口。相反，由于ApplicationContext扩展了MessageResource接口，因而具有消息处理的能力(i18N)，具体spring如何使用国际化，以后章节会详细描述。  

2.强大的事件机制(Event)  
   基本上牵涉到事件(Event)方面的设计，就离不开观察者模式。不明白观察者模式的朋友，最好上网了解下。因为，这种模式在java开发中是比较常用的，又是比较重要的。  
ApplicationContext的事件机制主要通过ApplicationEvent和ApplicationListener这两个接口来提供的，和java swing中的事件机制一样。即当ApplicationContext中发布一个事件的时，所有扩展了ApplicationListener的Bean都将会接受到这个事件，并进行相应的处理。  

Spring提供了部分内置事件，主要有以下几种：  
ContextRefreshedEvent ：ApplicationContext发送该事件时，表示该容器中所有的Bean都已经被装载完成，此ApplicationContext已就绪可用  
ContextStartedEvent：生命周期 beans的启动信号  
ContextStoppedEvent: 生命周期 beans的停止信号  
ContextClosedEvent：ApplicationContext关闭事件，则context不能刷新和重启，从而所有的singleton bean全部销毁(因为singleton bean是存在容器缓存中的)  

  虽然，spring提供了许多内置事件，但用户也可根据自己需要来扩展spriong中的事物。注意，要扩展的事件都要实现ApplicationEvent接口。  

3.底层资源的访问  
    ApplicationContext扩展了ResourceLoader(资源加载器)接口，从而可以用来加载多个Resource，而BeanFactory是没有扩展ResourceLoader  

4.对Web应用的支持  
   与BeanFactory通常以编程的方式被创建不同的是，ApplicationContext能以声明的方式创建，如使用ContextLoader。当然你也可以使用ApplicationContext的实现之一来以编程的方式创建ApplicationContext实例 。  

5.其它区别  
  1).BeanFactroy采用的是延迟加载形式来注入Bean的，即只有在使用到某个Bean时(调用getBean())，才对该Bean进行加载实例化，这样，我们就不能发现一些存在的Spring的配置问题。而ApplicationContext则相反，它是在容器启动时，一次性创建了所有的Bean。这样，在容器启动时，我们就可以发现Spring中存在的配置错误。  

  2).BeanFactory和ApplicationContext都支持BeanPostProcessor、BeanFactoryPostProcessor的使用，但两者之间的区别是：BeanFactory需要手动注册，而ApplicationContext则是自动注册

  ## 什么是Spring的依赖注入？

  依赖注入，是IOC的一个方面，是个通常的概念，它有多种解释。这概念是说你不用创建对象，而只需要描述它如何被创建。你不在代码里直接组装你的组件和服务，但是要在配置文件里描述哪些组件需要哪些服务，之后一个容器（IOC容器）负责把他们组装起来。

  ## 有哪些不同类型的IOC（依赖注入）方式

- 构造器依赖注入：构造器依赖注入通过容器触发一个类的构造器来实现的，该类有一系列参数，每个参数代表一个对其他类的依赖。
- Setter方法注入：Setter方法注入是容器通过调用无参构造器或无参static工厂 方法实例化bean之后，调用该bean的setter方法，即实现了基于setter的依赖注入。

## 在 Spring中如何注入一个java集合？