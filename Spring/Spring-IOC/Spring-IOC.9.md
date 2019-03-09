---
title: 加载Properties文件
tags: 
- 加载Properties文件
categories: 
- Spring 
---

加载Properties文件方式如下

```xml
 <context:property-placeholder location="classpath:foo.properties" />
```

获取值则使用
```xml
${key}
```
