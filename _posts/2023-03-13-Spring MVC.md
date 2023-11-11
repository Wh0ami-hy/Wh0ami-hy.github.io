---
layout: post   	
catalog: true 	
tags:
    - Spring
---



# 1. 什么是Spring MVC

MVC即模型（Model）、视图（View）、 控制器（Controller），是一种软件设计规范，将业务逻辑、数据、视图分离开的代码组织方式，降低了视图与业务逻辑间的双向偶合

Spring MVC 下一般把后端项目分为 Service 层（处理业务）、Dao 层（数据库操作）、Entity 层（实体类）、Controller 层（控制层，返回数据给前台页面），各层间的调用关系又如下：

**遵循Controller–Service接口–ServiceImpt实现类–Mapper接口模式**

就是用Spring MVC编写Controller处理请求，再将结果转换成json响应给客户端

# 2. Spring MVC的核心组件

DispatcherServlet ：**核心的中央处理器**，负责接收请求、分发，并给予客户端响应

HandlerMapping：**处理器映射器**，根据 uri 去匹配查找能处理的 Handler ，并会将请求涉及到的拦截器和 Handler 一起封装

HandlerAdapter：**处理器适配器**，根据 HandlerMapping找到的 Handler ，适配执行对应的 Handler

Handler（Controller）：**请求处理器**，处理实际请求的处理器，返回逻辑视图（即页面名称）

ViewResolver：**视图解析器**，根据 Handler 返回的逻辑视图，解析并渲染出真实的视图（即页面文件），并传递给 DispatcherServlet 响应客户端

# 3. Spring MVC工作原理（重点）

Spring的web框架围绕DispatcherServlet设计

**工作原理**

客户端（浏览器）发送请求， DispatcherServlet 拦截请求

DispatcherServlet 根据请求信息调用 HandlerMapping

HandlerMapping 根据 uri 去匹配查找能处理的 Handler（也就是我们平常说的 Controller 控制器） ，并会将请求涉及到的拦截器和 Handler 一起封装

DispatcherServlet 调用 HandlerAdapter适配执行 Handler

Handler 完成对用户请求的处理后，会返回一个 ModelAndView对象给DispatcherServlet，ModelAndView 包括数据模型和逻辑视图，通俗的讲就是页面中的数据和页面名称

ViewResolver 会根据逻辑 View 查找实际的 View（即根据页面名称去查找真实的页面文件）

DispaterServlet 把返回的 Model 传给 View（视图渲染）

把 View 返回给请求者（浏览器）

![springmvc工作原理](F:\笔记\博客\文章图片\springmvc工作原理.png)

# 4. 搭建SpringMVC

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

**使用springMVC必须配置的三大件：处理器映射器、处理器适配器、视图解析器**

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

编写业务Controller ，要么实现Controller接口，要么增加注解。需要返回一个ModelAndView

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

# 5. 用注解开发SpringMVC（重点）

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

**注意：@RestController和@Controller**

@RestController（Spring4+）相当于@Controller + @ResponseBody，返回json或者xml格式数据

@Controller 配合视图解析器 InternalResourceViewResolver使用，返回到指定页面

@RequestBody：接收的参数是来自requestBody（请求体）中。一般用于处理非Content-Type:application/x-www-form-urlencoded编码格式的数据，比如：application/json、application/xml 等类型的数据

**创建视图层**

同上

**@RequestMapping**

@RequestMapping注解用于映射url到控制器类或一个特定的处理程序方法。可用在类或方法上。用在类上，表示类中的所有响应请求的方法都是以该地址作为父路径

# 6. 使用RESTful风格

在Spring MVC中可以使用 `@PathVariable`注解，让方法参数的值对应绑定到一个URI模板变量上

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

可以通过 `@RequestMapping(value = "/hello",method = RequestMethod.GET)` 中的method实现不同请求方法的响应

也可以通过 `@GetMapping、@PostMapping、@PutMapping、@DeleteMapping、@PatchMapping`实现不同请求方法的响应

# 7. 重定向和转发

## 7.1. ModelAndView方式

设置ModelAndView对象，根据view的名称跳到指定的页面

页面：`视图解析器前缀 + viewName + 视图解析器后缀`

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

## 7.2. ServletAPI方式

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

## 7.3. SpringMVC方式（重点）

**通过SpringMVC来实现转发和重定向-无视图解析器**

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

视图解析器就是实现controller中return的值和视图解析器前缀、后缀的拼接

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
        return "redirect:index";
    }
}
```

## 7.4. 重定向与转发的区别

转发是在服务器端起作用的，当使用 forward() 方法时，Servlet 容器传递HTTP请求，从当前的 Servlet 或 JSP，此过程仍然在 request 的作用范围内。转发后，浏览器的地址栏内容不变

重定向是在用户的浏览器端工作的，是 Servlet 对浏览器做出响应后，浏览器再次发送一个新的请求。重定向后，浏览器的地址栏内容发生变化

重定向访问服务器两次，转发只访问服务器一次

转发只能转发到自己的web应用内，重定向可以重定义到任意资源路径

转发相当于服务器跳转，相当于方法调用，在执行当前文件的过程中转向执行目标文件，两个文件（当前文件和目标文件）属于同一次请求，前后页共用一个request，可以通过此来传递一些数据或者session信息，request.setAttribute()和 request.getAttribute()。而重定向会产生一个新的request，不能共享request域信息与请求参数

实际应用：用户登录功能

```java
@Controller
public class LoginController {
    // 访问登录页
    @RequestMapping("/toLogin")
    public String toLogin(){
        // 跳转到登录界面
        return "forward:/login.jsp";
    }
    // 处理登录页接收到的参数
    @RequestMapping(value = "login",method = RequestMethod.POST)
    public String doLogin(String username,String password){
        /* 验证参数，验证登录
            验证成功，则记录session信息，同时重定向到首页
            验证失败，则执行 return toLogin();
         */
        return toLogin();
    }
    // 用户退出登录
    @RequestMapping(value = "/logout",method = RequestMethod.GET)
    public String doLogout(HttpServletRequest request){
        // 销毁session对象
        request.getSession().invalidate();
        // 重定向到登录页面
        return "redirect:toLogin";
    }
}
```

# 8. 数据处理

## 8.1. 处理提交的数据

**提交的参数名称和后端方法中定义的参数名一致**

请求

```
提交数据 : http://localhost:8080/hello?name=hy
```

后端

```java
@RequestMapping("/hello")
public String hello(String name){
    System.out.println(name);
    return "hello";
}
```

**提交的参数名称和后端方法中定义的参数名不一致**

请求

```
提交数据 : http://localhost:8080/hello?username=hy
```

后端使用 `@RequestParam` 处理（建议不管一致不一致都处理一下）

```java
@RequestMapping("/hello")
public String hello(@RequestParam("username") String name){
    System.out.println(name);
    return "hello";
}
```

**提交的是一个对象**

假设前端传递的是一个对象User，则匹配User对象中的字段名。如果字段名一致则可以接收。如果字段名不一致则接收不到，结果为null

```java
@Controller
public class TestController {
    @RequestMapping(value = "/test",method = RequestMethod.GET)
    // 接收User对象
    public String test1(User user){
        //转发
        return "/WEB-INF/jsp/test.jsp";
    }
}
```

## 8.2. 数据回显到前端

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

注：ModelMap继承了LinkedHashMap。Model是精简版

# 9. 乱码问题解决

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

# 10. 返回JSON格式数据（重点）

JSON是JavaScript对象的字符串表示法，它使用文本表示一个JS对象的信息，**本质是一个字符串**

```json
{"键名":"值"}
```

**Jackson（推荐）**

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

# 11. SpringMVC拦截器

拦截器是SpringMVC框架的，只有使用了SpringMVC框架的工程才能使用，拦截器是AOP思想的具体应用

拦截器只会拦截访问的控制器方法，不会拦截静态资源，如果访问的是jsp/html/css/image/js 是不会进行拦截的

## 11.1. 拦截器的执行顺序

- 请求到达 DispatcherServlet
- DispatcherServlet 发送至 Interceptor ，执行 preHandle
- 请求达到 Controller
- 请求结束后，postHandle 执行

## 11.2. 自定义拦截器

![QQ截图20230107172231](F:\笔记\博客\文章图片\QQ截图20230107172231.png)

实现 HandlerInterceptor 接口即可，该接口包含三个方法，分别在请求处理的不同阶段调用

```java
public class HelloInterceptor implements HandlerInterceptor {

    // preHandle() 方法在请求处理之前调用，可以用于进行一些预处理操作
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("处理前");
        return true;
    }
	// postHandle() 方法在请求处理之后调用，在视图渲染之前调用，可以用于修改 ModelAndView 对象
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("处理后");
    }
	// afterCompletion() 方法在请求处理完成之后调用，可以用于进行一些资源清理操作，也可以用来统计请求耗时
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("清理");
    }
}
```

在xml 配置文件，配置启动拦截器 src/main/resources/springmvc-servlet.xml

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

在Javaconfig 配置类中，配置启动拦截器

```java
/*
addPathPatterns方法定义拦截的地址

excludePathPatterns定义排除某些地址不被拦截

添加的一个拦截器没有addPathPattern任何一个ur则默认拦截所有请求

如果没有excludePathPatterns任何一个请求，则默认不放过任何一个请求
*/
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors (InterceptorRegistry registry) {
        registry.addInterceptor( new LoginInterceptor()).addPathPatterns ("/user/**");
    }
}
```

## 11.3. 拦截器应用-登录验证

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

# 12. 文件上传和下载

## 12.1. 文件上传

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

采用file.transferTo 来保存上传的文件

**MultipartFile 和 CommonsMultipartFile的区别**

MultipartFile 和 CommonsMultipartFile 都是用来接收上传的文件流的MultipartFile是一个接口，CommonsMultipartFile是MultipartFile接口的实现类。使用MultipartFile 作为形参接收上传文件时，直接用即可。CommonsMultipartFile 作为形参接收上传文件时，必需添加@RequestParam注解，否则会报错

```java
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

## 12.2. 文件下载

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

# 13. 跨域请求

## 13.1. 使用 @CrossOrigin 注解

```java
@RestController
public class MyController {

  @CrossOrigin(origins = "http://example.com")
  @GetMapping("/my-endpoint")
  public String myEndpoint() {
    return "Hello, world!";
  }
}
```

在以上示例中，@CrossOrigin 注解将允许来自 `http://example.com`的跨域请求访问 `/my-endpoint` 端点。还可以使用 @CrossOrigin 注解的其他属性来更精细地控制跨域请求的行为

## 13.2. 使用配置类

如果需要在整个应用程序中启用跨域请求支持，你可以在 Spring MVC 配置类中使用 WebMvcConfigurer接口的 addCorsMappings方法

```java
@Configuration
public class MyConfig implements WebMvcConfigurer {

  @Override
  public void addCorsMappings(CorsRegistry registry) {
    registry.addMapping("/**")
      .allowedOrigins("http://example.com")
      .allowedMethods("GET", "POST")
      .allowedHeaders("header1", "header2", "header3")
      .exposedHeaders("header1", "header2")
      .allowCredentials(false)
      .maxAge(3600);
  }
}
```

# 14. 视图解析器

## 14.1. Spring MVC提供的视图解析器

InternalResourceViewResolver：用于解析JSP或HTML等资源文件

FreeMarkerViewResolver：用于解析 FreeMarker 模板

TilesViewResolver：用于解析 Tiles 布局

ContentNegotiatingViewResolver：复合视图解析器，可以根据请求的 Accept 头信息来选择对应的视图解析器进行解析

## 14.2. 自定义视图解析器

创建自定义的视图解析器类 ViewConfig ，并实现 ViewResolver 接口

在该类中实现 resolveViewName() 方法，用于根据视图名称解析视图

以下示例中，创建了一个名为 ViewConfig 的类，实现了 `ViewResolver` 接口，并重写了其中的 `resolveViewName()` 方法。在该方法中，检查逻辑视图名称是否以`"myview:"`  前缀开头。如果是，则创建一个名为 `MyView` 的自定义视图对象，并设置其 URL 属性为逻辑视图名称去掉前缀之后的部分。否则，返回 `null` 表示无法解析视图名称

```java
public class ViewConfig implements ViewResolver {

    @Override
    public View resolveViewName(String viewName, Locale locale) throws Exception {
        if (viewName.startsWith("myview:")) {
            String viewPath = viewName.substring("myview:".length());
            MyView view = new MyView();
            view.setUrl(viewPath);
            return view;
        } else {
            return null; // 返回 null 表示无法解析视图名称
        }
    }
}
```

在Javaconfig 配置类中，配置启动自定义视图解析器

```java
@Configuration
public class MyWebConfig implements WebMvcConfigurer {

    @Override
    public void configureViewResolvers(ViewResolverRegistry registry) {
        registry.viewResolver(new ViewConfig());
    }
}
```



















































