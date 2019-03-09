---
title: Servlet-Listener-分类
tags: 
- Servlet-Listener-分类
categories: 
- Servlet
---



# Servlet-Listener-分类

Servlet 2.5规范中，共有8种监听器接口和6种事件

一类是监控对象的创建与销毁
1. HttpSessionListener: 监控Session的创建与销毁
2. ServletContextListener: 监控context的创建与销毁
3. ServletRequestListener: 监控request的创建与销毁

一类是监控对象的属性变化
1. HttpSessionAttributeListener
2. ServletContextAttributeListener
3. ServletRequestAttributeListener

一类是监听Session对象
1. HttpSessionBindingListener
2. HttpSessionActivationListener

## 监控对象的创建与销毁
HttpSessionListener:
1. 创建Session时执行sessionCreated()
2. 超时或执行session.invalidate()时执行sessionDestroyed()
3. 可用于收集在线者的信息

ServletContextListener:
1. 服务器启动或者热部署war包时执行contextInitialized()方法
2. 服务器关闭或者只关闭该Web时执行contextDestroy()方法
3. 可用于启动时获取web.xml里的配置参数


ServletRequestListener:
1. 用户每次请求request都会执行requestInitailzed()方法
2. request处理完毕自动销毁前执行requestDestroy()方法
3. 注意一个HTML中如果有多个图片，一次请求会出发多个request事件

## 监控对象的属性变化

HttpSessionAttributeListener：
1. 添加属性：执行sessionAdded()
2. 更新属性：执行sessionReplaced()
3. 删除属性：执行sessionRemoved()

ServletContextAttributeListener
1. 添加属性：执行contextAdded()
2. 更新属性：执行contextReplaced()
3. 删除属性：执行contextRemoved()

ServletRequestAttributeListener
1. 添加属性：执行requestAdded()
2. 更新属性：执行requestReplaced()
3. 删除属性：执行requestRemoved()

## 监听Session对象

HttpSessionBindingListener:
1. 当对象被放到Session里时执行valueBound()
2. 当对象从Session里移除，执行valueUnbound()
3. 对象必须实现了Listener

HttpSessionActivationListener
1. 服务器关闭时，会将Session内容保存到硬盘上（钝化过程），会执行sessionWillPassivate()
2. 服务器启动时，会将Session内容从硬盘上重新加载,会执行sessionDidActivate()
3. 对象必须实现了Listener