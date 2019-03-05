---
title: SSM整合
date: 2018-02-28 00:00:01
tgas: 
- SSM整合
categories: 
- SpringMVC
---

整合思路
1. 整合持久层mapper，包括数据源、会话工程及mapper代理对象的整合；
2. 整合业务层Service，包括事务及service的bean的配置；
3. 整合表现层Controller，直接使用springmvc的配置。
4. Web.xml加载spring容器（包含多个XML文件）

Spring核心配置文件
1. applicationContext-dao.xml
2. applicationContext-service.xml
3. springmvc.xml

依赖项
1. spring（包括springmvc）、
2. mybatis、
3. mybatis-spring整合包、
4. 数据库驱动、
5. 第三方连接池
6. JSTL(非必需,主要用于JSP)
7. servlet-api

# 依赖

