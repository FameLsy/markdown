<!-- 简介 -->

# Apache Shiro

Shiro功能示意图：

![shiro](https://raw.githubusercontent.com/FameLsy/Images/master/shiro/shiro.png)


**Authentication**：  
身份认证 / 登录，验证用户是不是拥有相应的身份；

**Authorization**：  
授权，即权限验证，验证某个已认证的用户是否拥有某个权限；即判断用户是否能做事情，常见的如：验证某个用户是否拥有某个角色。或者细粒度的验证某个用户对某个资源是否具有某个权限；

**Session Manager**：  
会话管理，即用户登录后就是一次会话，在没有退出之前，它的所有信息都在会话中；会话可以是普通 JavaSE 环境的，也可以是如 Web 环境的；

**Cryptography**：  
加密，保护数据的安全性，如密码加密存储到数据库，而不是明文存储；

**Web Support**：  
Web 支持，可以非常容易的集成到 Web 环境；

**Caching**：  
缓存，比如用户登录后，其用户信息、拥有的角色 / 权限不必每次去查，这样可以提高效率；

**Concurrency**：  
shiro 支持多线程应用的并发验证，即如在一个线程中开启另一个线程，能把权限自动传播过去；

**Testing**：  
提供测试支持；

**Run As**：  
允许一个用户假装为另一个用户（如果他们允许）的身份进行访问；

**Remember Me**：  
记住我，这个是非常常见的功能，即一次登录后，下次再来的话不用登录了

# Shiro架构

Shiro架构示意图：

![shiro2](https://raw.githubusercontent.com/FameLsy/Images/master/shiro/shiro2.png)

**Subject**：  
主体，代表了当前 “用户”，这个用户不一定是一个具体的人，与当前应用交互的任何东西都是 Subject，如网络爬虫，机器人等；即一个抽象概念；所有 Subject 都绑定到 SecurityManager，与 Subject 的所有交互都会委托给 SecurityManager；可以把 Subject 认为是一个门面；SecurityManager 才是实际的执行者；

**SecurityManager**：  
安全管理器；即所有与安全有关的操作都会与 SecurityManager 交互；且它管理着所有 Subject；可以看出它是 Shiro 的核心，它负责与后边介绍的其他组件进行交互，如果学习过 SpringMVC，你可以把它看成 DispatcherServlet 前端控制器；

**Realm**：  
域，Shiro 从 Realm 获取安全数据（如用户、角色、权限），就是说 SecurityManager 要验证用户身份，那么它需要从 Realm 获取相应的用户进行比较以确定用户身份是否合法；也需要从 Realm 得到用户相应的角色 / 权限进行验证用户是否能进行操作；可以把 Realm 看成 DataSource，即安全数据源。

# 导入

```
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-core</artifactId>
    <version>1.2.2</version>
</dependency>
```