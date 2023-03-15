---
layout: post   	
catalog: true 	
tags:
    - Spring
---



# RESTful API

官网：https://restfulapi.cn/

它定义了互联网软件服务的架构原则，如果一个架构符合REST原则，则称之为RESTful架构。

REST并不是一个标准，它更像一组客户端和服务端交互时的架构理念和设计原则，基于这种架构理念和设计原则的Web API更加简洁，更有层次。

# RESTful特点

每一个URL代表一种资源

资源操作：使用POST、DELETE、PUT、GET，使用不同方法对资源进行操作。分别对应 添加、 删除、修改、查询

资源的表现形式是JSON或者HTML

客户端与服务端之间的交互在请求之间是无状态的，从客户端到服务端的每个请求都包含必需的信息

传统风格：通过不同的参数来实现不同的效果

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

