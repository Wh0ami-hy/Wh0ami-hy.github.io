---
layout: post   	
catalog: true 	
tags:
    - Spring
---



# 1. 什么是REST

官网：https://restfulapi.cn/

REST 是一种基于超媒体构建分布式系统的架构风格。 REST 独立于任何基础协议，并且不一定绑定到 HTTP

# 2. REST设计原则

REST API 围绕资源设计，资源是可访问的任何类型的对象、数据或服务。资源应基于名词（资源）而不是动词（对资源执行的操作）

```
https://example.com/customers/123/orders
```

在 URI 中采用一致的命名约定。对引用集合的 URI 使用复数名词。将集合和子项的 URI 组织成层次结构。 例如，`/customers` 是客户集合的路径，`/customers/5` 是 ID 为 5 的客户的路径。

避免请求复杂度超过 `集合/子项目/集合`的资源 URI

资源操作：使用POST、DELETE、PUT、GET，使用不同方法对资源进行操作。分别对应 添加、 删除、修改、查询

使用 JSON 作为交换格式，将参数信息都封装在 JSON中

客户端与服务端之间的交互在请求之间是无状态的，从客户端到服务端的每个请求都包含必需的信息

传统风格：通过携带不同的参数来实现不同的效果

```
http://localhost:8080/add?a=1&b=2
```

RESTful操作资源：可以通过不同的请求方式来实现不同的效果，即请求地址一样，但是可以实现不同功能

```
http://127.0.0.1/item/1 查询,GET
http://127.0.0.1/item 新增,POST
http://127.0.0.1/item 更新,PUT
http://127.0.0.1/item/1 删除,DELETE
```

常见的HTTP方法及其在RESTful风格下的使用：

| HTTP方法 | 操作   | 返回值     | 特定返回值 |
| -------- | ------ | ---------- | ---------- |
| POST     | Create | 201        | 404 or 409 |
| GET      | Read   | 200        | 200 or 404 |
| PUT      | Update | 200 or 204 | 404 or 405 |
| PATCH    | Update | 200 or 204 | 404        |
| DELETE   | Delete | 200        | 404 or 405 |

# 3. REST API最佳实践

仅使用get、post方法实现增删改查，put和delete方法可能不被服务器或防火墙支持

## 3.1. 查-GET

**查询列表**

```
url: /一级资源/二级资源/list
method: get
param: param
```

筛选条件相关的常见参数

```
?pageNum=1 第几页
?pageSize=10 每页几条数据
?offset=10：指定返回记录的开始位置。
?sortby=name&order=asc：指定返回结果按照哪个属性排序，以及排序顺序。
?type=1：其他筛选条件
```

**查询主键**

```
url: /一级资源/二级资源/{id}
method: get
param: param
```

## 3.2. 增-POST

**增加单个**

```
url: /一级资源/二级资源
method: post
data: data
```

**批量增加**

...

## 3.3. 改-POST

```
url: /一级资源/二级资源/edit
method: post
data: data
```

## 3.4. 删-GET

**删除单个**

```
url: /一级资源/二级资源/delete/{roomIds}
method: get
```

**批量删除**

roomIds是一个List，当其中只有一个值时，是删除单个，当其中有多个值时是批量删除