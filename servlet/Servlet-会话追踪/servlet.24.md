---
title: Servlet-session-有效期
tags: 
- Servlet-session-有效期
categories: 
- Servlet
---

# Servlet-session-有效期

Session的有效期
1. 由maxInactiveInterval属性控制,一旦超时(从不活跃的时间算起),Session就会被删除
2. Tomcat中session默认时间为20分钟
```java
aSession.setMaxInactiveInterval(int seconds);
aSession.getMaxInactiveInterval();
```

session还提供了立即失效的方法
```java
aSession.invalidate();
```

此外,session超时时间可以通过web.xml来设置
```xml
<!-- web.xml -->
<session-config>
<!-- 单位:分 -->
    <session-timeout>60</session-timeout>
</session-config>
```