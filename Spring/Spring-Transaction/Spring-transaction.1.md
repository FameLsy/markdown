---
title: Spring-事务管理-简介
tgas: 
-  Spring-事务管理-简介
categories: 
- Spring 
---

Spring并不直接管理事务，而是提供了多种事务管理器，他们将事务管理的职责委托给其他相关平台框架的事务来实现。  

Spring事务管理器的接口是PlatformTransactionManager，通过这个接口，Spring为各个平台如JDBC、Hibernate等都提供了对应的事务管理器，但是具体的实现就是各个平台自己的事情了。
