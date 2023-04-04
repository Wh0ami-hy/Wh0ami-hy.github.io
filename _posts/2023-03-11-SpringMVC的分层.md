---
layout: post   	
catalog: true 	
tags:
    - Spring
---



# 1.后端

## 1.1.拦截器（interceptor）

interceptor

## 1.2.数据访问层（mapper ）

dao、mapper，用于从数据库或其他数据存储介质中读取和写入数据

**先设计mapper接口，然后在xml配置文件中进行配置**

定义接口 UserMapper

```java
@Mapper
public interface UserMapper {
    List<User> queryUsers();
}
```

配置xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.hy.mapper.UserMapper">
    <!--id表示接口当中定义的方法. resultType,表示数据库查询的结果.-->
    <select id="queryUsers" resultType="com.hy.entity.User" parameterType="com.hy.entity.User">
        select * from login;
    </select>
</mapper>
```

## 1.3.拦截请求（controller ）

controller，用于处理用户请求和交互，并将请求转发给相应的服务类

调用service接口中的方法

```java
@RestController
@RequestMapping("/user")
public class UserController {
    // 注入service接口
    @Autowired
    private UserService userService;
    // 查询所有用户信息
    @RequestMapping(value = "/all", method = RequestMethod.GET)
    public List<User> test(){
        return userService.queryUsers();
    }
}
```

## 1.4.实现具体业务逻辑（service ）

service，用于执行业务逻辑，如计算、验证、授权等

一般由两部分组成 **一个Java接口和一个实现类**

service接口的内容对应mapper接口的内容。serviceimpl是把mapper和业务进行整合的文件

定义接口 UserService

```java
public interface UserService {
    // 对应于mapper层接口中定义的方法
    List <User> queryUsers();
}
```

定义接口实现类 UserServiceImpl

```java
@Component
public class UserServiceImpl implements UserService {
	// 注入mapper接口
    @Autowired
    private UserMapper userMapper;
    @Override
    public List<User> queryUsers() {
        return userMapper.queryUsers();
    }
}
```

## 1.5.工具类（utils ）

utils，用于提供常用的实用程序方法，如日期格式化、字符串处理、加密解密等

## 1.6.配置类（config ）

config，拓展功能、修改默认功能

## 1.7.实体类（entity）

entity、pojo，一般一个entity对应数据库中一个表，一般实现了JavaBean的标准，实体类要序列化

POJO细分：

PO：POJO在持久层的体现，对POJO持久化后就成了PO。PO更多的是跟数据库设计层面相关，一般PO与数据表对应，一个PO就是对应数据表的一条记录

BO：POJO在业务层的体现，对于业务操作来说，更多的是从业务上来包装对象，如一个User的BO，可能包括name, age, sex, privilege, group等，这些属性在数据库中可能会在多张表中，因为每一张表对应一个PO，而我们的BO需要这些PO组合起来(或说重新拼装)才能成为业务上的一个完整对象

VO：POJO在表现层的体现。 当我们处理完数据时，需要展现时，这时传递到表现层的POJO就成了VO。它就是为了展现数据时用的

DAO：PO持久化到数据库是要进行相关的数据库操作的(CRUQ)，这些对数据库操作的方法会统一放到一个Java对象中，这就是DAO

DTO：POJO在两个系统间传递时，一种方式就是将POJO序列化后传递，这个传递状态的POJO就是DTO

# 2.各层间调用关系

**要遵循Controller–Service接口–ServiceImpt实现类–Mapper接口模式**

controller调用service接口，serviceimpl实现类调用mapper接口并实现其他业务

在Service接口有多个ServiceImpl实现类的情况，就需要指定参数名来选择哪个ServiceImpt实现类

```java
Service层（此时有两个接口实现类）
    @Service("PCIImpt1")
    class PCIImpt1 imeplements PCI{}

    @Service("PCIimpt2")
    class PCIImpt2 imeplements PCI{}

Controller层
    @Resource(name="PCIimpt2")  //填PCIimpt1，注入PCIimpt1实现类，填PCIimpt2，则注入PCIimpt2实现类
    private PCI pci;    //注入接口以Resource手动指定接收
```

controller层调用了service层接口中的方法，service层由service对应的接口和实现类组成，serviceImpl实现service的相关接口同时完成相关的业务逻辑处理

service层的实现类serviceImpl 再调用mapper层的接口，进行业务逻辑应用的处理

mapper层的接口在对应的xml配置文件中进行配置、实现以及关联，mapper层的任务就是向数据库发送sql语句，完成数据的处理任务

![springmvc各层调用](F:\笔记\博客\文章图片\springmvc各层调用.png)

# 3.编写顺序

entity、mapper、service、controller

