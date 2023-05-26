---
layout: post   	
catalog: true 	
tags:
    - Spring

---




# 1. 后端代码结构

在Java 的web开发中，使用了Spring MVC 框架之后，我们的代码组织形式变成了controller，service，dao这种形式

## 1.1. 拦截器（interceptor）

interceptor

## 1.2. DAO层（mapper ）

实现数据层的操作

dao（Data Access Objects）、mapper，用于从数据库或其他数据存储介质中读取和写入数据

**先设计mapper接口，然后在xml配置文件中进行配置**

定义接口 UserMapper

```java
@Mapper
public interface UserMapper {
    List<User> queryUsers();
}
```

在xml中写SQL语句

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

## 1.3. 控制器（controller ）

controller

接收业务请求，并将请求转发至业务处理对象

接收业务请求处理结果，并将结果分发到响应页面

不执行实际的业务逻辑，调用service 接口中的方法去完成

控制器应该围绕用例、业务能力来设计

**默认情况下，控制器是单例**

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

## 1.4. 业务层（service ）

service，用于执行业务逻辑，如计算、验证、授权等

一般由两部分组成 **一个Java接口和一个实现类**

service接口的内容对应mapper接口的内容。serviceImpl是把mapper和业务进行整合的文件

最好围绕业务功能、领域、用例（无论你怎么称呼都行）来构建服务，合理的使用单一职责原则

如 `AccountService`, `UserService` 这样的服务，比起 `DatabaseService`、`ValidationService` 这样的会更合适一些

理想的情况是Controller和Service之间的一对一映射

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

## 1.5. 工具类（utils ）

utils，用于提供常用的实用程序方法，如日期格式化、字符串处理、加密解密等

## 1.6. 配置类（config ）

config，拓展功能、修改默认功能

## 1.7. POJO类

### 1.7.1. enity

entity层用于定义实体，定义各个属性以及各个属性的getter()和setter()方法，与数据库中的属性值基本保持一致

### 1.7.2. domain

域是一个大范围，如简历域包括工作经验表、项目经验表、简历基本信息表。 在domain包中，就可以定义一个大的简历对象，将三个表的内容整合在一个对象中，作为整体操作

### 1.7.3. model（BO）

当用model当包名时，一般里面存的是实体类的模型，**是用来给后端用的**

比如user表中有name、id、age，出于安全原因，我们需要把用户的密码定义在另一表中，即user_passwd表，但进行相关操作时，我们往往需要将两个表关联使用，每次定义都很麻烦。因此可以在model层中定义user_model类，将user表中的信息与user_passwd表中的信息整合成一张综合表，这样在进行操作时只需调用综合表，就可以完成对两个表的关联操作

### 1.7.4. view（VO）

当用view当包名时，一般里面存放的是对实体表的映射类（视图类），**是用来给前端用的**
。有时候我们仅仅需要获得某一个表的几个字段，所以此时可以用view存储这几个字段

### 1.7.5. DTO

DTO = Data Transfer Object = 数据传输对象，与view的用法相同，不过是叫法不同

**注意**：一个POJO类一般实现了JavaBean的标准，要在各个实体类中实现序列化接口，避免在微服务中出现错误

## 1.8. 各层间调用关系

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

## 1.9. 编写顺序

POJO、mapper、service、controller

# 2. Spring Boot的最佳实践

## 2.1. 使用自定义BOM来维护第三方依赖

可以借鉴Spring IO Platform来编写自己的基础项目platform-bom，所有的业务模块项目应该以BOM的方式引入。这样在升级第三方依赖时，就只需要升级这一个依赖的版本而已
```xml
<dependencyManagement>
   <dependencies>
       <dependency>
           <groupId>io.spring.platform</groupId>
           <artifactId>platform-bom</artifactId>
           <version>Cairo-SR3</version>
           <type>pom</type>
           <scope>import</scope>
       </dependency>
   </dependencies>
</dependencyManagement>
```

在平时的练习项目中，为了比避免在pom.xml文件中重复导包的问题，可以自己创建一个父工程，将所有的依赖版本统一，避免出现jar包冲突和每个项目都要导包的问题。在创建项目时只需要继承父工程即可。类似于spring boot的父工程

## 2.2. 使用自动化配置

自动化配置是Spring Boot的一部分，它可以简化你的代码并使之工作。当在类路径上检测到特定的jar文件时，自动配置就会被激活。

使用它的最简单方法是依赖Spring Boot Starters

如果想与Redis进行集成

```xml
<dependency>  
   <groupId>org.springframework.boot</groupId>  
   <artifactId>spring-boot-starter-data-redis</artifactId>  
</dependency>
```

高级一点：为开发组中常见的问题，创建自己的自动配置，即**手动实现一个 starter**

## 2.3. 正确设计代码目录结构

参照**阿里巴巴开发手册**

```
├── main
│   ├── java
│   │   └── com
│   │       └── bestpractice
│   │           ├── config
│   │           ├── constants
│   │           ├── controller
│   │           ├── dao
│   │           ├── exception
│   │           ├── filter
│   │           ├── manager
│   │           ├── model
│   │           │   ├── bo
│   │           │   ├── dto
│   │           │   └── vo
│   │           ├── service
│   │           │   ├── impl
│   │           ├── task
│   │           ├── utils
│   │           └── BestPracticeApplication.java
│   │
│   └── resources
```

## 2.4. 保持@Controller的简洁

了解设计REST API的最佳实践

## 2.5. 围绕业务功能构建@Service

最好围绕业务功能/领域/用例（无论你怎么称呼都行）来构建服务。

可以决定使用Controler和Service之间的一对一映射，那将是理想的情况。但这并不意味着，Service之间不能互相调用！

## 2.6. 提供全局异常处理

Spring Boot提供了两种主要方法：

使用 HandlerExceptionResolver 定义全局异常处理策略

在控制器上添加 @ExceptionHandler 注解，这在某些特定场景下使用可能会很有用

## 2.7. 使用日志框架

应该使用Logger进行日志记录，而不是使用System.out.println()手动执行

在Spring Boot中完成，几乎没有配置。只需获取该类的记录器实例：

```java
Logger logger = LoggerFactory.getLogger(MyClass.class);
```

## 2.8. 代码测试

测试切片，你可以根据需要仅连接部分应用程序

## 2.9. 合理使用Swagger等接口工具

在大多数情况下，其他应用程序将通过REST API 调用你的应用程序。因此我们需要维护一份API文档。文档应该由代码生成。当然有一些工具可以做到这一点。其中最受欢迎的是Swagger

## 2.10. 使数据库独立于核心业务逻辑之外

理想情况下，你不希望服务知道它正在与哪个数据库通信，这需要一些抽象来封装对象的持久性

**一些优秀的持久层框架可以帮助我们做到这些**

## 2.11. 使用Spring Initializr来开始一个新的Spring Boot项目

使用Initializr创建应用程序可确保你获得经过测试和验证的依赖项，这些依赖项适用于Spring自动配置。你甚至可能会发现一些新的集成




参考：

https://www.52pojie.cn/forum.php?mod=viewthread&tid=1644312&extra=page%3D1%26filter%3Dtypeid%26typeid%3D192