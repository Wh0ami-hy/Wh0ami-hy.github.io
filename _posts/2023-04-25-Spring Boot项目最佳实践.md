---
layout: post   	
catalog: true 	
tags:
    - Spring

---




# 1. Spring Boot通用项目结构

在Java 的web开发中，使用了Spring MVC 框架之后，我们的代码组织形式变成了controller，service，dao这种形式

以Spring Boot 构建的项目应该总体分为三大层

-   `项目根目录/src/main/java`：放置项目Java源代码
-   `项目根目录/src/main/resources`：放置项目静态资源和配置文件
-   `项目根目录/src/test/java`：放置项目测试用例代码

## 1.1. `/src/main/java`目录下的结构

不同项目和团队实践不一样，稍许有区别，但整体安排应该差不多。如果是**多模块**的项目的话，下面的结构应该只对应其中一个模块，其他模块的代码组织也大致差不多

```
|_annotation：放置项目自定义注解
|_aspect：放置切面代码
|_config：放置配置类
|_constant：放置常量、枚举等定义
   |__consist：存放常量定义
   |__enums：存放枚举定义
|_controller：放置控制器代码
|_filter：放置一些过滤、拦截相关的代码
|_mapper：放置数据访问层代码接口
|_model：放置数据模型代码
   |__entity：放置数据库实体对象定义
   |__dto：存放数据传输对象定义
   |__vo：存放显示层对象定义
|_service：放置具体的业务逻辑代码（接口和实现分离）
   |__intf：存放业务逻辑接口定义
   |__impl：存放业务逻辑实际实现
|_util：放置工具类和辅助代码
```

## 1.2. `/src/main/resources`目录下的结构

主要存放静态配置文件和页面静态资源等东西

```
|_mapper：存放mybatis的XML映射文件（如果是mybatis项目）
|_static：存放网页静态资源，比如下面的js/css/img
   |__js：
   |__css：
   |__img：
   |__font：
   |__等等
|_template：存放网页模板，比如thymeleaf/freemarker模板等
   |__header
   |__sidebar
   |__bottom
   |__XXX.html等等
|_application.yml       基本配置文件
|_application-dev.yml   开发环境配置文件
|_application-test.yml  测试环境配置文件
|_application-prod.yml  生产环境配置文件
```


## 1.3. 拦截器（interceptor）

interceptor

## 1.4. DAO层（mapper）

实现数据层的操作

dao（Data Access Objects）层也叫mapper层，用于从数据库或其他数据存储介质中读取和写入数据

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
        select * from login
    </select>
</mapper>
```

## 1.5. 控制器（controller）

controller 接收请求，并将请求转发至业务处理对象

接收业务请求处理结果，并将结果分发到响应页面

不执行实际的业务逻辑，调用service 接口中的方法去完成

控制器应该围绕用例、业务能力来设计

**controller层一般返回封装的结果类`Result`**

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

## 1.6. 业务层（service）

service，用于执行业务逻辑，如计算、验证、授权等

一般由两部分组成 **一个Java接口和一个实现类**

serviceImpl是实现具体业务逻辑的，通常是把mapper和业务进行整合

**service层一般返回操作的执行结果（成功、失败），处理后的业务数据（如经过计算、过滤或转换的数据）、异常或错误信息**

最好围绕业务功能、领域、用例来构建服务，合理的使用单一职责原则

如 `AccountService`, `UserService` 这样的服务，比起 `DatabaseService`、`ValidationService` 这样的会更合适一些

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

## 1.7. 工具类（utils）

utils，用于提供常用的实用程序方法，如日期格式化、字符串处理、加密解密等

## 1.8. 配置类（config）

config，拓展功能、修改默认功能

## 1.9. 数据模型定义（model）

model包中放的都是POJO类，一个POJO类一般实现了JavaBean的标准，要在各个实体类中实现序列化接口，避免在微服务中出现错误

`entity`是数据库实体对象定义，定义各个属性以及各个属性的getter()和setter()方法，与数据库表结构一一对应

《阿里巴巴Java开发手册》中定义如下（实际项目开发时，没有必要刻意照搬去定义这么多层对象）

`DO（Data Object）`：与数据库表结构一一对应，通过DAO层向上传输数据源对象

`DTO（Data Transfer Object）`：数据传输对象，Service层向外传输的对象

`BO（Business Object）`：业务对象。由Service层输出的封装业务逻辑的对象

`AO（Application Object）`：应用对象。在Web层与Service层之间抽象的复用对象模型，极为贴近展示层，复用度不高

`VO（View Object）`：显示层对象，通常是Web向模板渲染引擎层传输的对象

`Query`：数据查询对象，各层接收上层的查询请求。注意超过2个参数的查询封装，禁止使用Map类来传输

**实际开发中一般只涉及如下两种数据模型就够用了**

`DTO/VO` 泛指控制层与服务层之间的数据传输对象

`DO/Entity` 泛指服务层与DAO层间的数据传输对象

## 1.10. 各层间调用关系

**要遵循Controller–>Service接口–>ServiceImpt实现类–>Mapper接口->SQL->DataBase的调用模式**

**每当我们拿到一个新的项目到手时，只要按照这个思路去看别人项目的代码，应该基本都是能理得顺的**。

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

## 1.11. 编写顺序

先设计数据库表，然后定义好model，之后再实现其他部分

# 2. Spring Boot多模块项目结构

```
api
common
admin
system
```



# 3. Spring Boot的最佳实践

## 3.1. 使用自定义BOM来维护第三方依赖

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

在项目中，为了比避免在pom.xml文件中重复导包的问题，可以自己创建一个父工程，将所有的依赖版本统一，避免出现jar包冲突和每个项目都要导包的问题。在创建项目时只需要继承父工程即可。类似于spring boot的父工程

## 3.2. 使用自动化配置

自动化配置是Spring Boot的一部分，它可以简化你的代码并使之工作。当在类路径上检测到特定的jar文件时，自动配置就会被激活。

使用它的最简单方法是依赖Spring Boot Starters

如果想与Redis进行集成

```xml
<dependency>  
   <groupId>org.springframework.boot</groupId>  
   <artifactId>spring-boot-starter-data-redis</artifactId>  
</dependency>
```

可为开发中常见的问题，创建自己的自动配置，即**手动实现一个 starter**

## 3.3. 正确设计代码目录结构

```
├── main
│   ├── java
│   │   └── com
│   │       └── bestpractice
│   │           ├── config 存放配置类
│   │           ├── constants 放置常量、枚举等定义
│   │           ├── controller 放置控制器代码
│   │           ├── dao 放置数据访问层代码接口
│   │           ├── exception
│   │           ├── filter 放置一些过滤、拦截相关的代码
│   │           ├── manager 放置数据访问层代码接口
│   │           ├── model
│   │           │   ├── entity 放置数据库实体对象定义
│   │           │   ├── dto 存放数据传输对象定义
│   │           │   └── vo 存放显示层对象定义
│   │           ├── service 放置具体的业务逻辑代码（接口和实现分离）
│   │           │   ├── impl 存放业务逻辑实际实现
│   │           ├── task
│   │           ├── utils 放置工具类和辅助代码
│   │           └── BestPracticeApplication.java
│   │
│   └── resources
```

## 3.4. 保持Controller层的简洁

了解设计REST API的最佳实践

`Controller`层里可以做参数校验、异常抛出等操作，但建议不要放太多业务逻辑，业务逻辑尽量放到`Service`层代码中去做

## 3.5. 围绕业务功能构建Service

`Service`层做实际业务逻辑，完整的业务逻辑包含验证、缓存等

功能模块`Service`之间引用时，建议不要渗透到`DAO`层（或者`mapper`层），基于`Service`层进行调用和复用比较合理

业务逻辑层`Service`和数据库`DAO`层的操作对象不要混用。`Controller`层的数据对象不要直接渗透到`DAO`层（或者`mapper`层）；同理数据表实体对象`Entity`也不要直接传到`Controller`层进行输出或展示。

## 3.6. 使用全局异常处理

Spring Boot提供了两种主要方法：

使用 `HandlerExceptionResolver` 定义全局异常处理策略

在控制器上添加 `@ExceptionHandler` 注解，这在某些特定场景下使用可能会很有用

## 3.7. 使用 slf4j 日志

应该使用日志框架进行日志记录，而不是使用`System.out.println()`手动执行。建议将 Slf4j 与 Spring Boot 中默认的日志框架 logback 一起使用。可以使用 Lombok @Slf4j 注释非常轻松地创建日志记录器

在Spring Boot中完成，几乎没有配置。只需获取该类的记录器实例：

```java
Logger logger = LoggerFactory.getLogger(MyClass.class);
```

## 3.8. 代码测试

测试切片，你可以根据需要仅连接部分应用程序

## 3.9. 使用Swagger接口文档工具

在大多数情况下，其他应用程序将通过REST API 调用你的应用程序。因此我们需要维护一份API文档。文档应该由代码生成。

## 3.10. 使数据库独立于核心业务逻辑之外

理想情况下，你不希望服务知道它正在与哪个数据库通信，这需要一些抽象来封装对象的持久性

**一些优秀的持久层框架可以帮助我们做到这些**

## 3.11. 使用Spring Initializr来开始一个新的Spring Boot项目

使用Initializr创建应用程序可确保你获得经过测试和验证的依赖项，这些依赖项适用于Spring自动配置。

## 3.12. 使用 Lombok

Lombok 是一个 Java 库，可用于减少代码并允许我们使用其注释编写干净的代码

## 3.13. 避免空指针异常

- 为了避免 NullPointerException，我们可以使用 java.util 包中的 Optional。
- 我们还可以使用空安全库。例如：Apache Commons StringUtils
- 对已知对象调用 equals() 和 equalsIgnoreCase() 方法。
- 使用 valueOf() 而不是 toString()
- 使用基于 IDE 的 @NotNull 和 @Nullable 注释

## 3.14. 使用集合框架的最佳实践

- 对我们的数据集使用适当的集合。
    
- 将 forEach 与 Java 8 功能结合使用，并避免使用旧版 for 循环。
    
- 使用接口类型而不是实现。
    
- 使用 isEmpty() 而不是 size() 以获得更好的可读性。
    
- 不返回空值，可以返回空集合。
    
- 如果我们使用对象作为要存储在基于哈希的集合中的数据，则应重写 equals() 和 hashCode() 方法。请查看这篇文章“HashMap 内部是如何工作的”。

## 3.15. 使用分页

使用物理分页
## 3.16. 使用自定义响应对象
