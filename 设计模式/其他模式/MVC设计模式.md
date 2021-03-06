---
title: MVC设计模式
date: 2019 01-01 00:00:01
tags: 
- MVC设计模式
- 非23种设计模式
categories: 
- 设计模式
---

# 三层结构

三层架构包括：表现层、业务层、持久层

表现层：
1. 即web 层。它负责接收客户端请求，向客户端响应结果，通常客户端使用http 协议请求web 层，web 需要接收 http 请求，完成 http 响应。
2. 表现层包括展示层和控制层：控制层负责接收请求，展示层负责结果的展示。
3. 表现层依赖业务层，接收到客户端请求一般会调用业务层进行业务处理，并将处理结果响应给客户端。
4. 表现层的设计一般都使用 MVC 模型

业务层：
1. 即service 层。它负责业务逻辑处理，和我们开发项目的需求息息相关。web 层依赖业务层，但是业务层不依赖 web 层。
2. 业务层在业务处理时可能会依赖持久层，如果要对数据持久化需要保证事务一致性。（即事务应该放到业务层来控制）

持久层：
1. 即dao 层。负责数据持久化，包括数据层即数据库和数据访问层
2. 数据库是对数据进行持久化的载体，数据访问层是业务层和持久层交互的接口，业务层需要通过数据访问层将数据持久化到数据库中。
3. 通俗的讲，持久层就是和数据库交互，对数据库表进行曾删改查的。

# MVC设计模式

MVC：
1. 全名是 Model View Controller，是模型(model)－视图(view)－控制器(controller)的缩写
2. MVC 是表现层的设计模型，和其他层没有关系


Model（模型）：模型包含业务模型和数据模型，数据模型用于封装数据，业务模型用于处理业务。

View（视图）：通常指的就是我们的 jsp 或者 html。作用一般就是展示数据的。通常视图是依据模型数据创建的。

Controller（控制器）：是应用程序中处理用户交互的部分。作用一般就是处理程序逻辑的。

至于具体实现，可参考SpringMVC