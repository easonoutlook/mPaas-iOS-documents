@cnName 1 概述
@priority 1

# 1 概述

[TOC]

## 1.1 客户端框架设计思想

* Framework 的核心设计思想是将业务隔离成相对独立的模块，并着力追求模块与模块之间高內聚，低耦合。
* 以`是否有 UI 界面`作为标准，Framework 将不同的`模块`划分为`微应用/Micro Application`和`服务/Service`，并定义了它们的基类`DTMicroApplication`和`DTService`，进行生命周期的管理。
* Application 是独立的业务流程； Service 则是提供通用服务；
* Service 有状态，一旦启动后，其在整个客户端的生命周期中一直存在；
* Service 在后台执行，没有 UI 元素；

## 1.2 客户端框架职责

* 管理应用生命周期，UIApplication 代理事件处理与分发；
* 管理 Micro Application 与 Service，处理应用的跳转与生命周期；
* 处理客户端 Crash 捕获与上报；
* 提供 DTViewController、DTSchemeHandler 等基类；

## 1.3 钱包客户端架构图

其中 Mobile Framework 为客户端框架
![arch](https://t.alipayobjects.com/images/rmsweb/T1MpJgXd8uXXXXXXXX.png)

## 1.4 Micro Application 与 Service 管理 UML 图

![uml](https://t.alipayobjects.com/images/rmsweb/T1baJgXfdgXXXXXXXX.png)