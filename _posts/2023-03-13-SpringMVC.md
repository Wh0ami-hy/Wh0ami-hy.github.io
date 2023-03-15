---
layout: post   	
catalog: true 	
tags:
    - Spring
---



# 什么是MVC

MVC是模型（Model）、视图（View）、 控制器（Controller）的简写，是一种软件设计规范

是将业务逻辑、数据、显示分离的方法来组织代码

MVC主要作用是降低了视图与业务逻辑间的双向偶合

MVC不是一种设计模式，MVC是一种架构模式。不同的MVC存在差异

# SpringMVC执行原理

Spring的web框架围绕DispatcherServlet设计。DispatcherServlet的作用是将请求分发到不同的处理器。从Spring 2.5开始，使用Java 5或者以上版本的用户可以采用基于注解的controller声明方式

SpringMVC就是要求我们编写一个个Controller处理请求，将结果转换成json响应给客户端

1. DispatcherServlet表示前置控制器，是整个SpringMVC的控制中心。用户发出请求，DispatcherServlet接收请求并拦截请求
2. HandlerMapping（处理器映射）由DispatcherServlet自动调用，HandlerMapping根据请求url查找Handler
3. HandlerExecution表示具体的Handler，其主要作用是根据url查找控制器
4. HandlerExecution将解析后的信息传递给DispatcherServlet，如解析控制器映射等
5. HandlerAdapter表示处理器适配器，其按照特定的规则去执行Handler
6. Handler让具体的Controller执行
7. Controller将具体的执行信息返回给HandlerAdapter，如ModelAndView
8. HandlerAdapter将视图逻辑名或模型传递给DispatcherServlet
9. DispatcherServlet调用视图解析器(ViewResolver)来解析HandlerAdapter传递的逻辑视图名
10. 视图解析器将解析的逻辑视图名传给DispatcherServlet
11. DispatcherServlet根据视图解析器解析的视图结果，调用具体的视图
12. 最终视图呈现给用户

# 搭建SpringMVC

创建一个maven项目，添加web的支持

```
org.apache.maven.archetypes:maven-archetype-webapp
```

pom.xml中添加依赖

```xml
<dependencies>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.1.9.RELEASE</version>
    </dependency>
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>4.0.1</version>
    </dependency>
    <dependency>
        <groupId>javax.servlet.jsp</groupId>
        <artifactId>jsp-api</artifactId>
        <version>2.2</version>
    </dependency>
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>jstl</artifactId>
        <version>1.2</version>
    </dependency>
</dependencies>
```

配置 src/main/webapp/WEB-INF/web.xml，注册DispatcherServlet

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <!--注册DispatcherServlet-->
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--关联一个springmvc的配置文件:【servlet-name】-servlet.xml-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc-servlet.xml</param-value>
        </init-param>
        <!--启动级别-1-->
        <load-on-startup>1</load-on-startup>
    </servlet>
   <!--    springmvc中 /的含义：只匹配所有请求，不匹配jsp页面
                   	  /*的含义：匹配所有请求，包括jsp页面
    -->
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```

编写SpringMVC的配置文件 src/main/resources/springmvc-servlet.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.3.xsd">

<!--    springmvc核心三要素-->
<!--    处理器映射器-->
    <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
<!--    处理器适配器-->
    <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
<!--    视图解析器 模板引擎 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="internalResourceViewResolver">
<!--        前缀    -->
        <property name="prefix" value="/WEB-INF/"/>
<!--        后缀-->
        <property name="suffix" value=".jsp"/>
    </bean>
</beans>
```

**创建Controller**

编写业务Controller ，要么实现Controller接口，要么增加注解。需要返回一个ModelAndView，装数据，封视图

控制器负责解析用户的请求并将其转换为一个模型

注意：以接口的方式实现控制器的缺点是一个控制器中只有一个方法，如果要多个方法则需要定义多个Controller

```java
public class HelloController implements Controller {
    @Override
    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception{
        //ModelAndView 模型和视图
        ModelAndView mv = new ModelAndView();
        //封装对象，放在ModelAndView中
        mv.addObject("msg","HelloSpringMVC!");
        //封装要跳转的视图，放在ModelAndView中
        mv.setViewName("hello"); // 跳转到/WEB-INF/jsp/hello.jsp
        return mv;
    }
}
```

注册bean，在 src/main/resources/springmvc-servlet.xml

```xml
<!-- /hello表示访问的是/hello.jsp -->
<bean id="/hello" class="com.hy.controller.HelloController"/>
```

**创建视图层**

编写jsp页面，src/main/webapp/WEB-INF/jsp/hello.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>hello</title>
</head>
<body>
<h1>hello springmvc</h1>>
${msg}
</body>
</html>
```

**配置Tomcat运行**

启动tomcat

若遇到 IDEA控制台乱码问题，则打开tomcat下的 conf/logging.properties 修改 java.util.logging.ConsoleHandler.encoding = GBK

除此以外，其他地方的所有编码都设为UTF-8

**可能遇到的问题：访问出现404，排查步骤：**

1. 查看控制台输出，看一下是不是缺少了什么jar包
2. 如果jar包存在，显示无法输出，就在IDEA的项目发布中，添加lib依赖
3. 重启Tomcat 即可解决

# 用注解开发SpringMVC

使用springMVC必须配置的三大件：处理器映射器、处理器适配器、视图解析器。其中处理器映射器、处理器适配器可用注解代替

pom.xml 同上

src/main/webapp/WEB-INF/web.xml 同上

src/main/webapp/WEB-INF/jsp/hello.jsp 同上

配置 src/main/resources/springmvc-servlet.xml：

1. 让IOC的注解生效：设置自动扫描包的功能
2. 静态资源过滤 ：HTML . JS . CSS . 图片 ， 视频 …..
3. MVC的注解驱动
4. 配置视图解析器

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/mvc
        https://www.springframework.org/schema/mvc/spring-mvc.xsd">
    <!-- 自动扫描包，让指定包下的注解生效,由IOC容器统一管理 -->
    <context:component-scan base-package="com.hy.controller"/>
    <!-- 让Spring MVC不处理静态资源 -->
    <mvc:default-servlet-handler />
    <!--
    支持mvc注解驱动
        在spring中一般采用@RequestMapping注解来完成映射关系
        要想使@RequestMapping注解生效
        必须向上下文中注册DefaultAnnotationHandlerMapping
        和一个AnnotationMethodHandlerAdapter实例
        这两个实例分别在类级别和方法级别处理。
        而annotation-driven配置帮助我们自动完成上述两个实例的注入。
     -->
    <mvc:annotation-driven />
    <!-- 视图解析器 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
          id="internalResourceViewResolver">
        <!-- 前缀 -->
        <property name="prefix" value="/WEB-INF/jsp/" />
        <!-- 后缀 -->
        <property name="suffix" value=".jsp" />
    </bean>
</beans>
```

**创建Controller**

编写业务Controller

```java
@Controller // 让Spring IOC容器初始化时自动扫描到
public class HelloController {
    @RequestMapping("/hello")
    public String sayHello(Model model){
        //向模型中添加属性msg与值，可以在JSP页面中取出并渲染
        model.addAttribute("msg","hello,SpringMVC");
        // 跳转到 /WEB-INF/jsp/hello.jsp
        return "hello";
    }
}
```

注意：@RestController和@Controller

@RestController（Spring4+）相当于@Controller + @ResponseBody，返回json或者xml格式数据

@Controller 配合视图解析器InternalResourceViewResolver使用，返回到指定页面

**创建视图层**

同上

**@RequestMapping**

@RequestMapping注解用于映射url到控制器类或一个特定的处理程序方法。可用在类或方法上。用在类上，表示类中的所有响应请求的方法都是以该地址作为父路径

# 使用RESTful风格

在Spring MVC中可以使用 @PathVariable注解，让方法参数的值对应绑定到一个URI模板变量上

```java
@Controller
public class RestFulController {
    //映射访问路径
    @RequestMapping("/commit/{p1}/{p2}")
    public String index(@PathVariable int p1, @PathVariable int p2, Model model){
        int result = p1+p2;
        //Spring MVC会自动实例化一个Model对象用于向视图中传值
        model.addAttribute("msg", "结果："+result);
        //返回视图位置
        return "test";
    }
}
```

响应请求的实现方式

1. 可以通过 @RequestMapping(value = "/hello",method = RequestMethod.GET) 中的method实现不同请求方法的响应
2. 也可以通过 @GetMapping、@PostMapping、@PutMapping、@DeleteMapping、@PatchMapping实现不同请求方法的响应

# 重定向和转发

**ModelAndView方式**

设置ModelAndView对象，根据view的名称和视图解析器跳到指定的页面

页面：{视图解析器前缀} + viewName + {视图解析器后缀}

```xml
<!--    视图解析器 模板引擎 -->
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="internalResourceViewResolver">
    <!--        前缀    -->
    <property name="prefix" value="/WEB-INF/"/>
    <!--        后缀-->
    <property name="suffix" value=".jsp"/>
</bean>
```

对应的controller类

```java
public class ControllerTest implements Controller {
    public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
        //返回一个模型视图对象
        ModelAndView mv = new ModelAndView();
        mv.addObject("msg","ControllerTest");
        mv.setViewName("test");
        return mv;
    }
}
```

**ServletAPI方式**

通过设置ServletAPI，不需要视图解析器

通过HttpServletResponse进行输出、实现重定向、实现转发

```java
@Controller
public class ResultGo {
    @RequestMapping("/result/t2")
    public void test2(HttpServletRequest req, HttpServletResponse rsp) throws IOException {
        // 重定向
        rsp.sendRedirect("/index.jsp");
    }
    @RequestMapping("/result/t3")
    public void test3(HttpServletRequest req, HttpServletResponse rsp) throws Exception {
        //转发
        req.setAttribute("msg","/result/t3");
        req.getRequestDispatcher("/WEB-INF/jsp/test.jsp").forward(req,rsp);
    }
}
```

**SpringMVC方式（重点）**

通过SpringMVC来实现转发和重定向，无需视图解析器

```java
@Controller
public class ResultSpringMVC {
    @RequestMapping("/rsm/t1")
    public String test1(){
        //转发
        return "/index.jsp";
    }
    @RequestMapping("/rsm/t2")
    public String test2(){
        //转发二
        return "forward:/index.jsp";
    }
    @RequestMapping("/rsm/t3")
    public String test3(){
        //重定向
        return "redirect:/index.jsp";
    }
}
```

**通过SpringMVC来实现转发和重定向 - 有视图解析器**

视图解析器就是实现controller中return的值和xml中前缀、后缀的拼接

```java
@Controller
public class ResultSpringMVC2 {
    @RequestMapping("/rsm2/t1")
    public String test1(){
        //转发
        return "test";
    }
    @RequestMapping("/rsm2/t2")
    public String test2(){
        //重定向
        return "redirect:/index.jsp";
    }
}
```

# 数据处理

## 处理提交的数据

**提交的域名称和处理方法的参数名一致**

请求

```
提交数据 : http://localhost:8080/hello?name=hy
```

直接接收

```java
@RequestMapping("/hello")
public String hello(String name){
    System.out.println(name);
    return "hello";
}
```

**提交的域名称和处理方法的参数名不一致**

请求

```
提交数据 : http://localhost:8080/hello?username=hy
```

使用 @RequestParam 处理（建议不管一致不一致都处理一下）

```java
@RequestMapping("/hello")
public String hello(@RequestParam("username") String name){
    System.out.println(name);
    return "hello";
}
```

**提交的是一个对象**

接收前端用户传递的参数，判断参数的名字，假设该名字直接在方法参数中，则可以直接接收

假设传递的是一个对象User，则匹配User对象中的字段名。如果字段名一致则可以接收，否则接收不到，结果为null

```java
@Controller
public class TestController {
    @RequestMapping(value = "/test",method = RequestMethod.GET)
    public String test1(User user){
        //转发
        return "/WEB-INF/jsp/test.jsp";
    }
}
```

## 数据回显到前端

**通过ModelAndView**

```java
public class ControllerTest1 implements Controller {
    public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
        //返回一个模型视图对象
        ModelAndView mv = new ModelAndView();
        mv.addObject("msg","ControllerTest1");
        mv.setViewName("test");
        return mv;
    }
}
```

**通过ModelMap**

```java
@RequestMapping("/hello")
public String hello(@RequestParam("username") String name, ModelMap model){
    //封装要显示到视图中的数据
    model.addAttribute("name",name);
    System.out.println(name);
    return "hello";
}
```

**通过Model**

```java
@RequestMapping("/hello")
public String hello(@RequestParam("username") String name, Model model){
    //封装要显示到视图中的数据
    model.addAttribute("msg",name);
    System.out.println(name);
    return "hello";
}
```

注意：

ModelMap：继承了LinkedHashMap

Model：精简版

# 乱码问题解决

在 src/main/webapp/WEB-INF/web.xml中配置

```xml
<filter>
    <filter-name>encoding</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>utf-8</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>encoding</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```



# 返回JSON格式数据

JSON是JavaScript对象的字符串表示法，它使用文本表示一个JS对象的信息，本质是一个字符串

JSON格式

```json
{"键名":"值"}
```

## Jackson

导入依赖 pom.xml

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.10.2</version>
</dependency>
```

转为 json格式

```java
ObjectMapper mapper = new ObjectMapper();

String str = mapper.writeValueAsString(...);
```

解决 json乱码问题，在src/main/resources/springmvc-servlet.xml 上添加一段消息StringHttpMessageConverter转换配置

```xml
<mvc:annotation-driven>
    <mvc:message-converters register-defaults="true">
        <bean class="org.springframework.http.converter.StringHttpMessageConverter">
            <constructor-arg value="UTF-8"/>
        </bean>
        <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
            <property name="objectMapper">
                <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                    <property name="failOnEmptyBeans" value="false"/>
                </bean>
            </property>
        </bean>
    </mvc:message-converters>
</mvc:annotation-driven>
```

尝试封装一个JsonUtils类 专门实现JSON格式的转换

## FastJson

导入依赖 pom.xml

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.62</version>
</dependency>
```

转为 json格式

```java
String str = JSON.toJSONString(user);
```

乱码解决方法同上

# SpringMVC拦截器

SpringMVC的处理器拦截器类似于Servlet开发中的过滤器Filter，用于对处理器进行预处理和后处理。开发者可以自己定义一些拦截器来实现特定的功能

拦截器是AOP思想的具体应用

**过滤器**

servlet规范中的一部分，任何java web工程都可以使用

**拦截器**

拦截器是SpringMVC框架自己的，只有使用了SpringMVC框架的工程才能使用

拦截器只会拦截访问的控制器方法，如果访问的是jsp/html/css/image/js是不会进行拦截的，即不会拦截静态资源

## 自定义拦截器

实现 HandlerInterceptor 接口即可

```java
public class HelloInterceptor implements HandlerInterceptor {
    /* 前置拦截器（第一道拦截）
        return true则放行，去执行下一个拦截器
        return false则不放行
    */
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("处理前");
        return true;
    }
	//第二道拦截
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("处理后");
    }
	//第三道拦截
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("清理");
    }
}
```

配置启动拦截器 src/main/resources/springmvc-servlet.xml

```xml
<!--拦截器配置-->
<mvc:interceptors>
    <mvc:interceptor>
        <!-- /**表示过滤这个请求下的所有请求-->
        <mvc:mapping path="/**"/>
        <!--实现拦截的类-->
        <bean class="com.hy.interceptor.HelloInterceptor"/>
    </mvc:interceptor>
</mvc:interceptors>
```

## 拦截器应用-登录验证

三个页面：

1. main.jsp（首页，提供跳转到登录页的功能，默认不拦截）
2. login.jsp（登录信息提交页，实现登录信息输入功能，默认不拦截）
3. hello.jsp（登录后才可以自由访问的页面，默认拦截，必须登录后访问）

main.jsp

```jsp
<html>
<body>
<a href="${pageContext.request.contextPath}/doLogin">Please Login</a>
<a href="${pageContext.request.contextPath}/hello">MyPage</a>
<a href="${pageContext.request.contextPath}/logout">LogOut</a>
</body>
</html>
```

login.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<h1>登录页面</h1>
<hr>
<body>
<form action="${pageContext.request.contextPath}/login" method="post">
    name:<input type="text" name="username"> <br>
    pwd:<input type="password" name="password"> <br>
    <input type="submit" value="提交">
</form>
</body>
</html>
```

hello.jsp

```jsp
<html>
<body>
<h2>welcome ${name}! you are logining</h2>
</body>
</html>
```

控制器

```java
@Controller
public class LoginController {
    // 首页
    @RequestMapping(value = "/main",method = RequestMethod.GET)
    public String main(){
        return "main";
    }
    // 登录页
    @RequestMapping(value = "/doLogin",method = RequestMethod.GET)
    public String doLogin(){
        return "login";
    }
    // 处理登录信息
    @RequestMapping(value = "/login",method = RequestMethod.POST)
    public String hello(Model model,HttpSession session, @RequestParam("username") String username, @RequestParam("password") String password){
        // 接收登录信息，把用户信息存在session中
        session.setAttribute("userLoinInfo",username);
        // 传递信息到视图
        model.addAttribute("name",username);
        return "hello";
    }
    // 欢迎页面
    @RequestMapping(value = "/hello",method = RequestMethod.GET)
    public String doHello(){
        return "hello";
    }
    // 注销功能，将用户信息从session中删除
    @RequestMapping(value = "/logout",method = RequestMethod.GET)
    public String doLogout(HttpSession session){
        session.removeAttribute("userLoinInfo");
        return "login";
    }
}
```

拦截器

```java
public class LoginInterceptor implements HandlerInterceptor {
    /* 判断是否登录
       判断依据：session中是否有用户信息
    */
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        HttpSession session = request.getSession();
        // 访问 /hello时，若已经登录，则放行，否则拦截
        if (session.getAttribute("userLoinInfo") != null && request.getRequestURI().contains("/hello")) {
            System.out.println("已放行");
            return true;
        }
        // 访问 /doLogin 则放行
        if (request.getRequestURI().contains("/doLogin")) {
            System.out.println("已放行");
            return true;
        }
        // 访问 /main 则放行
        if (request.getRequestURI().contains("/main")){
            System.out.println("已放行");
            return true;
        }
        // 访问 /login 则放行
        if (request.getRequestURI().contains("/login")){
            System.out.println("已放行");
            return true;
        }
        // 访问 /logout 则放行
        if (request.getRequestURI().contains("/logout")){
            System.out.println("已放行");
            return true;
        } else {
            System.out.println("已拦截");
            return false;
        }
    }
}
```

配置拦截器 src/main/resources/springmvc-servlet.xml

```xml
<!--拦截器配置-->
<mvc:interceptors>
    <mvc:interceptor>
        <!--            /**表示过滤这个请求下的所有请求-->
        <mvc:mapping path="/**"/>
        <!--实现拦截的类-->
        <bean class="com.hy.interceptor.LoginInterceptor"/>
    </mvc:interceptor>
</mvc:interceptors>
```

# 文件上传和下载

## 文件上传

Spring MVC为文件上传提供了直接的支持，这种支持是用 MultipartResolver实现的

导入依赖 pom.xml

```xml
<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.3.3</version>
</dependency>
```

编写上传页面 file.jsp

前端表单要求：为了能上传文件，必须将表单的method设置为POST，并将enctype设置为multipart/form-data。只有在这样的情况下，浏览器才会把用户选择的文件以二进制数据发送给服务器

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>file</title>
</head>
<body>
<form action="${pageContext.request.contextPath}/upload" enctype="multipart/form-data" method="post">
    <input type="file" name="file" />
    <input type="submit" value="upload" />
</form>
</body>
</html>
```

文件上传配置

注意：这个bean的id必须为 multipartResolver，否则上传文件会报400的错误

```xml
<!--文件上传配置-->
<bean id="multipartResolver"  class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
    <!-- 请求的编码格式，必须和jSP的pageEncoding属性一致，以便正确读取表单的内容，默认为ISO-8859-1 -->
    <property name="defaultEncoding" value="utf-8"/>
    <!-- 上传文件大小上限，单位为字节（10485760=10M） -->
    <property name="maxUploadSize" value="10485760"/>
    <property name="maxInMemorySize" value="40960"/>
</bean>
```

CommonsMultipartFile 的常用方法：

1. String getOriginalFilename()：获取上传文件的原名
2. InputStream getInputStream()：获取文件流
3. void transferTo(File dest)：将上传文件保存到一个目录文件中

采用file.Transto 来保存上传的文件

```JAVA
@Controller
public class UploadController {
    // 文件上传页面
    @RequestMapping(value = "/file",method = RequestMethod.GET)
    public String file(){
        return "file";
    }
    // 处理上传的文件
    @ResponseBody
    @RequestMapping(value = "/upload",method = RequestMethod.POST)
    public String fileUpload(@RequestParam("file") CommonsMultipartFile file, HttpServletRequest request) throws Exception{
        // 设置上传路径
        String path = request.getSession().getServletContext().getRealPath("/upload");
        // 创建上传路径的文件夹
        File realPath = new File(path);
        if (!realPath.exists()) {
            realPath.mkdir();
        }
        // 打印上传文件地址
        System.out.println("fileSavePath:\t" + realPath);
        // 通过CommonsMultipartFile的方法直接写文件
        file.transferTo(new File(realPath + "/" +file.getOriginalFilename()));
        return "success";
    }
}
```

## 文件下载

步骤：

1. 设置 response 响应头
2. 读取文件 — InputStream
3. 写出文件 — OutputStream
4. 执行操作
5. 关闭流 （先开后关）

down.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>down</title>
</head>
<body>
<a href="${pageContext.request.contextPath}/down">downPage</a>
</body>
</html>
```

控制器

```java
@Controller
public class DownController {
    @RequestMapping(value = "/getDown",method = RequestMethod.GET)
    public String getDown(){
        return "down";
    }
    @RequestMapping(value = "/down",method = RequestMethod.GET)
    public String down(HttpServletResponse response, HttpServletRequest request) throws Exception{
        // 要下载的图片的地址
        String path = request.getServletContext().getRealPath("/static");
        // 图片名字
        String filename = "15.jpg";
        //设置response响应头
        response.reset(); // 设置页面不缓存
        response.setCharacterEncoding("UTF-8");
        response.setContentType("multype/form-data");
        // 设置响应头
        response.setHeader("Content-Disposition", "attachment;filename=" + filename);
        response.setContentType("image/jpeg");
        // 以流的形式下载文件
        File file = new File(path,filename);
        // 读取文件：输入流
        InputStream input = new FileInputStream(file);
        // 写出文件：输出流
        OutputStream out = response.getOutputStream();
        byte[] body = new byte[1024];
        int index=0;
        // 执行写出操作
        while ((index=input.read(body))!=-1){
            out.write(body,0,index);
            out.flush();
        }
        out.close();
        input.close();
        return null;
    }
}
```





















































