---
layout: post   	
catalog: true 	
tags:
    - Java
---





# 创建项目

## 简介

SpringBoot是由Pivotal团队提供的全新框架，其设计目的是用来简化Spring应用的初始搭建以及开发过程

SpringBoot程序优点：

遵循“约定优于配置”的原则，只需要很少的配置或使用默认的配置。

能够使用内嵌的Tomcat、Jetty服务器， 不需要部署war文件。

提供定制化的启动器Starters，简化Maven配置，开箱即用。

纯Java配置，没有代码生成，也不需要XML配置。

提供了生产级的服务监控方案，如安全监控、应用监控、健康检测等。

## Idea创建boot项目

选择 2.x版本的springboot

社区版：安装插件`Spring Assistant`

Spring Initializr server Custom 修改为国内的

```
http://start.springboot.io
```

选择依赖包：spring web就是spring MVC、mysql driver数据库驱动、MyBatis framework 封装JDBC配置更简单。

springboot 内置 tomcat不需要添加tomcat

![springboot项目配置](F:\笔记\博客\文章图片\springboot项目配置-1656496587986.png)

![选择boot所需的框架](F:\笔记\博客\文章图片\选择boot所需的框架.png)

## 项目结构

![boot项目结构](F:\笔记\博客\文章图片\boot项目结构.png)

仅用到`src`，`pom.xml`

## 开发环境热部署

在实际的项目开发调试过程中会频繁地修改后台类文件，导致需要重新编译、重新启动，整个过程非常麻烦，影响开发效率。

Spring Boot提供了spring-boot-devtools组件,使得无须手动重启Spring
Boot应用即可重新编译、启动项目，大大缩短编译启动的时间。

devtools会监听classpath下的文件变动，触发Restart类加载器重新加载该类，从而实现类文件和属性文件的热部署。

并不是所有的更改都需要重启应用(如静态资源、视图模板)，可以通过设置spring.devtools.restart.exclude属性来指定一些文件或目录的修改不用重启应用

### pom.xml配置

在pom.xml配置文件中添加dev-tools依赖。
使用optional=true表示依赖不会传递，即该项目依赖devtools，其他项目如果引入此项目生成的JAR包，则不会包含devtools

```xml
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-devtools</artifactId>
<optional>true</optional>
</dependency>
```

### application.properties配置

在application.properties中配置devtools

```java
#热部署生效
spring.devtools.restart.enabled=true
#设置重启目录
spring.devtools.restart.additional-paths=src/main/java
#设置classpath目录 下的WEB-INF文件夹内容修改不重启
spring.devtools.restart.exclude=static/**
```

### idea配置

如果使用了Eclipse，那么在修改完代码并保存之后，项目将自动编译并触发重启

如果使用了IntelliJ IDEA，还需要配置项目自动编译。
打开Settings页面，在左边的菜单栏依次找到
Build,Execution,Deployment-→Compile,勾选Build project automatically

按Ctr|+ Shift+ A 快捷键，搜索 Registry，勾选
compiler.automake.allow.when.app.running复选框。

做完这两步配置之后，若开发者再次在Intellij IDEA中修改代码，则项目会自动重启。

# web开发

## 控制器

创建package：controller（存放方法类）

Spring Boot提供了@Controller和@RestController两种注解来标识此类负责接收和处理HTTP请求。

如果请求的是页面和数据，使用@Controller注解即可。如果只是请求数据，则可以使用@RestController注解。

@Controller通常与Thymeleaf模板引擎结合使用。（前后端不分离）

默认情况下，@RestController注解会将返回的对象数据转换为JSON格式。

![QQ截图20230107145148](F:\笔记\博客\文章图片\QQ截图20230107145148.png)

## 路由映射

@RequestMapping注解主要负责URL的路由映射。它可以添加在Controller类或者具体的方法上。

如果添加在Controller类上，则这个Controller中的所有路由映射都将会加上此映射规则

如果添加在方法上，则只对当前方法生效。

@RequestMapping注解包含很多属性参数来定义HTTP的请求映射规则。常用的属性参数如下：

```java
value：请求URL的路径，支持URL模板、正则表达式
method：HTTP请求方法。Method匹配也可以使用@GetMapping、@PostMapping等注解代替。
consumes：请求的媒体类型(Content- Type)，如application/json
produces：响应的媒体类型
params，headers：请求的参数及请求头的值
```

## 参数传递

@RequestParam：将请求参数绑定到控制器的方法参数上，接收的参数来自HTTP请求体（post）或请求url的QueryString（get），当请求的参数名称与Controller的业务方法参数名称一致时，@RequestParam可以省略，否则需写

```java
(@RequestParam(value = "user",required = false) String name)
```

@PathVaraible：用来处理动态的URL，URL的值可以作为控制器中处理方法的参数。

```java
{URL的动态部分}
```

@RequestBody：接收的参数是来自requestBody中，即请求体。一般用于处理非Content-Type:application/x-www-form-urlencoded编码格式的数据，比如：application/json、application/xml 等类型的数据

## 实体类

创建package：entity（存放实体类）

后端接收前端传的参数过多时，需要用到实体类来接收更方便，需要注意的是：实体类中对象的属性名要和前端的参数名一致，类型也要一致才能正确接收。

实体类中包含：private 属性、set和get方法（在idea中右键generate，选择，setter、getter，可快速生成get和set方法）

## 跨域请求

在请求方法前添加 @CrossOrigin注解

## 静态资源访问

使用IDEA创建Spring Boot项目，会默认创建出classpath:/static/目录， 静态资源一般放在这个目录下即可。

如果默认的静态资源过滤策略不能满足开发需求，也可以自定义静态资源过滤策略。

在application.properties中直接定义过滤规则和静态资源位置：

强烈建议不要更改

```
#默认静态资源目录
spring.mvc.static-path-pattern=/static/**
#自定义静态资源目录
spring.web.resources.static-locations=classpath:/static/
```

过滤规则为/static/**，静态资源位置为classpath:/static/

## 文件上传

表单的enctype属性规定在发送到服务器之前应该如何对表单数据进行编码。

当表单的enctype= "application/x-www-form-urlencoded" (默认)时,
form表单中的数据格式为: key=value&key=value

当表单的enctype="multipart/form-data"时，其传输数据形式浏览器会自动封装

Spring Boot工程嵌入的tomcat限制了请求的文件大小，每个文件的配置最大为1Mb，单次请求的文件的总数不能大于10Mb。

要更改这个默认值需要在配置文件(application.properties) 中加入两个配置

```
spring.servlet.multipart.max-file-size=10MB
spring.servlet.multipart.max-request-size=10MB
```

当表单的enctype= "multipart/form-data"时，可以使用MultipartFile获取上传的文件数据，再通过transferTo方法将其写入到磁盘中

## 拦截器

创建package：interceptor（存放拦截器）

拦截器在Web系统中很常见，对于某些全局统一的操作， 我们可以把它提取
到拦截器中实现。总结起来，拦截器大致有以下几种使用场景：

```
权限检查:如登录检测，进入处理程序检测是否登录，如果没有，则直接返回
登录页面。
性能监控:有时系统在某段时间莫名其妙很慢,可以通过拦截器在进入处理程
序之前记录开始时间，在处理完后记录结束时间，从而得到该请求的处理时间
通用行为:读取cookie得到用户信息并将用户对象放入请求，从而方便后续流
程使用，还有提取Locale、Theme信息等，只要是多个处理程序都需要的，即
可使用拦截器实现。
```

### 拦截器创建

Spring Boot定义了HandlerInterceptor接口来实现自定义拦截器的功能
HandlerInterceptor接口定义了preHandle、postHandle、afterCompletion三种方法，通过重写这三种方法实现请求前、请求时、请求后等操作

![QQ截图20230107172231](F:\笔记\博客\文章图片\QQ截图20230107172231.png)

### 拦截器注册

创建package：config（存放配置类）

addPathPatterns方法定义拦截的地址

excludePathPatterns定义排除某些地址不被拦截

添加的一个拦截器没有addPathPattern任何一个ur则默认拦截所有请求

如果没有excludePathPatterns任何一个请求，则默认不放过任何一个请求。

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors (InterceptorRegistry registry) {
        registry.addInterceptor( new LoginInterceptor()).addPathPatterns ("/user/**");
    }
}
```

## RESTfulApi

```
https://restfulapi.cn/
```

它定义了互联网软件服务的架构原则，如果一个架构符合REST原则，则称之为RESTful架构。REST并不是一个标准，它更像一组客户端和服务端交互时的架构理念和设计原则，基于这种架构理念和设计原则的Web API更加简洁，更有层次。

### RESTful特点

每一个URL代表一种资源

客户端使用GET、POST、 PUT、DELETE四种表示操作方式的动词对服务端资源进行操作：GET用于获取资源，POST用于新建资源(也可以用于更新资源)，PUT用于更新资源，DELETE用于删除资源。

通过操作资源的表现形式来实现服务端请求操作。

资源的表现形式是JSON或者HTML。

客户端与服务端之间的交互在请求之间是无状态的，从客户端到服务端的每个请求都包含必需的信息。

符合RESTful规范的Web API需要具备如下两个关键特性：

```
安全性：安全的方法被期望不会产生任何副作用，当我们使用GET操作获取资源时，不会引起资源本身的改变，也不会引起服务器状态的改变。
幂等性：幂等的方法保证了重复进行一个请求和一次请求的效果相同(并不是指响应总是相同的，而是指服务器上资源的状态从第一次请求后就不再改变了)，在数学上幂等性是指N次变换和一 次变换相同。
```

常见的HTTP方法及其在RESTful风格下的使用：

| HTTP方法 | 操作   | 返回值     | 特定返回值 |
| -------- | ------ | ---------- | ---------- |
| POST     | Create | 201        | 404 or 409 |
| GET      | Read   | 200        | 200 or 404 |
| PUT      | Update | 200 or 204 | 404 or 405 |
| PATCH    | Update | 200 or 204 | 404        |
| DELETE   | Delete | 200        | 404 or 405 |

### Spring Boot实现RESTful API

Spring Boot提供的spring-boot-starter-web组件完全支持开发RESTful API，提供了与REST操作方式(GET、POST、 PUT、 DELETE) 对应的注解。

```java
@GetMapping：处理GET请求，获取资源。
@PostMapping：处理POST请求，新增资源。
@PutMapping：处理PUT请求，更新资源。
@DeleteMapping：处理DELETE请求， 删除资源。
@PatchMapping：处理PATCH请求， 用于部分更新资源。
```

在RESTful架构中，每个网址代表一种资源， 所以URL中建议不要包含动词，只包含名词即可，而且所用的名词往往与数据库的表格名对应。

## Swagger

Swagger是一个规范和完整的框架，用于生成、描述、调用和可视化RESTful风格的Web服务，是非常流行的API表达工具。

Swagger能够自动生成完善的RESTful API文档，，同时并根据后台代码的修改同步更新，同时提供完整的测试页面来调试API。

### 添加依赖

在Spring Boot项目中集成Swagger同样非常简单，只需在项目中引入
springfox-swagger2和springfox.swagger-ui依赖即可。

```xml
<dependency>
<groupId> io.springfox</groupId>
<artifactId>springfox-swagger2</artifactId>
<version>2.9.2</version>
</dependency>
<dependency>
<groupId>io.springfox</groupId>
<artifactId>springfox-swagger-ui</artifactId>
<version>2.9.2</version>
</dependency>
```

### 添加Swagger配置类

```java
@Configuration //告诉Spring 容器，这个类是一个配置类
@EnableSwagger2 //启用Swagger2 功能

public class SwaggerConfig {
    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2)
                //apiInfo()是自定义方法
                .apiInfo(apiInfo())
                .select()
                // com 包下所有API 都交给Swagger2管理
                .apis(RequestHandlerSelectors.basePackage("com"))
                .paths(PathSelectors.any()).build();
    }

    // API 文档页面显示信息
    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("演示项目API") //标题
                .description("学习Swagger2的演示项目") //描述
                .build();
    }
}
```

### 接口测试

启动项目访问，即可打开自动生成的可视化测试页面

```
http://127.0.0.1:8080/swagger-ui.html
```

注意：Spring Boot 2.6.X后与Swagger有版本冲突问题，需要在
application.properties中加入以下配置：

```
spring.mvc.pathmatch.matching-strategy=ant_path_ matcher
```

注意：springframework  2.x版本，可以实现与 swagger2 的版本匹配。

## JWT跨域认证

### 加入依赖

```xml
		<dependency>
			<groupId>io.jsonwebtoken</groupId>
			<artifactId>jjwt</artifactId>
			<version>0.9.1</version>
		</dependency>
```

### 生成Token

创建文件夹 utils（存放工具类）

```java
    //生成token
    public static String generateToken(String username) {
        Date now = new Date();
        Date expiration = new Date(now.getTime() + 1000 * expire);
        return Jwts.builder()
                .setHeaderParam("type", "JWT")
                .setSubject(username)
                .setIssuedAt(now)
                .setExpiration(expiration)
                .signWith(SignatureAlgorithm.HS512, secret)
                .compact();
    }
```

### 解析Token

```java
    //解析token
    public static Claims getClaimsByToken(String token) {
        return Jwts.parser()
                .setSigningKey(secret)
                .parseClaimsJws(token)
                .getBody();

    }

```

# 部署spring boot

## 打包jar

打开idea，点击右上角 maven，再点击Lifecycle，再点击package即可打包

## 可能报错

解决spring-boot-maven-plugin爆红，添加version，版本要与spring-boot-starter-parent的version一致

```xml
				<artifactId>spring-boot-maven-plugin</artifactId>
				<version>2.7.7</version>
```

若报错如下

```
Failed to execute goal org.apache.maven.plugins:maven-resources-plugin:3.2.0
```

则先禁用 maven 中 的test

再pom.xml中添加如下（引入 maven-resources-plugin）

```xml
<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-resources-plugin</artifactId>
				<version>3.1.0</version>
			</plugin>
```

## 部署到服务器

```
nohup java -jar shop-0.0.1-SNAPSHOT.jar > logName.log 2>&1 &
```

注：nohup命令：不挂起，即关闭终端，程序继续运行

## 修改端口

application开头的配置文件中

```
server.port=8088
```

## junit

```
1.测试的依赖SpringBoot项目在创建的时候会自动加入
2.测试类会自动生成
3.业务类需要手动编写，要注意如果将来可以直接在测试类中使用 必须让这个类被Spring容器监管，只需要在业务类中加入@Service即可
4.在测试类中使用@AutoWared注解不用new的情况下就把业务层的对象创建出来了
5.运行测试类即可
```







