@cnName 1 Overview
@priority 1

# 1 Overview

[TOC]

## 1.1 Design concept of client framework

* The core designing concept of the framework is to separate the business into relatively independent modules to achieve high cohesion and loose coupling between modules. 
* The framework divides modules with or without UI interfaces into the 'Micro Application' or 'Service' respectively, and defines their base classes, namely 'DTMicroApplication' and 'DTService', for life cycle management. 
* Application is an independent business procedure, while Service offers universal services. 
* Service has a status. Once it is initiated, it always exists throughout the life cycle of the client. 
* Service is executed in the background and has no UI elements. 

## 1.2 Roles of client framework 

* Managing the application life cycle, and event processing and dispatching of the UIApplication delegates. 
* Managing Micro Application and Service and handling application life cycle and navigation to other applications. 
* Handling crash capture and reporting on the client. 
* Providing DTViewController, DTSchemeHandler and other base classes. 

## 1.3 Client architecture of wallet

In the figure, the Mobile Framework is the client framework. 
![arch](https://t.alipayobjects.com/images/rmsweb/T1MpJgXd8uXXXXXXXX.png)

## 1.4 UML diagram of Micro Application and Service management

![uml](https://t.alipayobjects.com/images/rmsweb/T1baJgXfdgXXXXXXXX.png)

