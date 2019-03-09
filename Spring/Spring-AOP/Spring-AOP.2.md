---
title: Spring-AOP-术语
tgas: 
- Spring-AOP-术语
categories: 
- Spring 
---


aop术语：
1. Joinpoint(连接点)   -- 所谓连接点是指那些被拦截到的点。在spring中,这些点指的是方法,因为spring只支持方法类型的连接点
2. Pointcut(切入点)        -- 所谓切入点是指我们要对哪些Joinpoint进行拦截的定义
3. Advice(通知/增强)    -- 所谓通知是指拦截到Joinpoint之后所要做的事情就是通知.通知分为前置通知,后置通知,异常通知,最终通知,环绕通知(切面要完成的功能)
4. Introduction(引介) -- 引介是一种特殊的通知在不修改类代码的前提下, Introduction可以在运行期为类动态地添加一些方法或Field
5. Target(目标对象)     -- 代理的目标对象
6. Weaving(织入)      -- 是指把增强应用到目标对象来创建新的代理对象的过程
7. Proxy（代理）       -- 一个类被AOP织入增强后，就产生一个结果代理类
8. Aspect(切面)        -- 是切入点和通知的结合，以后咱们自己来编写和配置的
9. Advisor（通知器、顾问）		--和Aspect很相似