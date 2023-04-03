---
layout: post   	
catalog: true 	
tags:
    - Spring
---



# 1.后端

## 1.1.拦截器

interceptor

## 1.2.数据访问层

dao == mapper 

## 1.3.拦截请求

controller 

## 1.4.实现具体业务逻辑

service 

## 1.5.公共包

utils 

写自己封装的工具类

## 1.6.配置类

config 

## 1.7.实体类

entity、pojo，一般一个entity对应数据库中一个表，一般实现了JavaBean的标准，实体类要序列化

POJO细分：

PO：POJO在持久层的体现，对POJO持久化后就成了PO。PO更多的是跟数据库设计层面相关，一般PO与数据表对应，一个PO就是对应数据表的一条记录

BO：POJO在业务层的体现，对于业务操作来说，更多的是从业务上来包装对象，如一个User的BO，可能包括name, age, sex, privilege, group等，这些属性在数据库中可能会在多张表中，因为每一张表对应一个PO，而我们的BO需要这些PO组合起来(或说重新拼装)才能成为业务上的一个完整对象

VO：POJO在表现层的体现。 当我们处理完数据时，需要展现时，这时传递到表现层的POJO就成了VO。它就是为了展现数据时用的

DAO：PO持久化到数据库是要进行相关的数据库操作的(CRUQ)，这些对数据库操作的方法会统一放到一个Java对象中，这就是DAO

DTO：POJO在两个系统间传递时，一种方式就是将POJO序列化后传递，这个传递状态的POJO就是DTO

# 2.各层间调用关系

![springmvc调用关系](F:\笔记\博客\文章图片\springmvc调用关系.png)

