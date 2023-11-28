---
layout: post   	
catalog: true 	
tags:
    - Spring
---





# 1. SpringBoot整合Thymeleaf

使用Thymeleaf作为视图解析器

**SpringBoot默认不支持 JSP，需要引入第三方模板引擎技术实现页面渲染**

导入依赖，之后将html放在 `src/main/resources/templates` 目录下，即可通过controller访问html

添加依赖 pom.xml

```xml
<!--Thymeleaf-->
<dependency>
    <groupId>org.thymeleaf</groupId>
    <artifactId>thymeleaf-spring5</artifactId>
</dependency>
<dependency>
    <groupId>org.thymeleaf.extras</groupId>
    <artifactId>thymeleaf-extras-java8time</artifactId>
</dependency>
```

配置类中设置，把thymeleaf 添加到 Spring Boot 上下文

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Bean
    public ClassLoaderTemplateResolver templateResolver() {
        ClassLoaderTemplateResolver templateResolver =
                new ClassLoaderTemplateResolver();
        templateResolver.setPrefix("/templates/");
        templateResolver.setSuffix(".html");
        templateResolver.setCharacterEncoding("UTF-8");

        return templateResolver;
    }

    @Bean
    public SpringTemplateEngine templateEngine() {
        SpringTemplateEngine templateEngine = new SpringTemplateEngine();
        templateEngine.setTemplateResolver(templateResolver());
        return templateEngine;
    }
}
```

controller层，使用Model 向前端传递数据

```java
@RequestMapping(value = "/index")
public String index(Model model){  //这里不仅仅可以是Model，还可以是Map、ModelMap
    model.addAttribute("name", "yyds");
    return "index";
}
```

在yml配置文件中关闭 SpringBoot 静态文件缓存，默认为true

当为true时，修改静态文件（html、css、js）需要重启服务器才可以有效，当为false时，修改静态文件（html、css、js）只要在浏览器端刷新就可以了

```yml
spring.thymeleaf.cache = false
```

**补充：thymeleaf语法**

在html文件中添加引用

```html
<html lang="en" xmlns:th="http://www.thymeleaf.org">
```

基础模板

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
<meta charset="UTF-8">
<title>Title</title>
</head>
<body>
<!--所有的html元素都可以被thymeleaf 替换接管: th:元素名-->
<div th:text="${msg}"></div>
</body>
</html>
```

所有的html元素都可以被thymeleaf替换接管

```html
th:元素名
```

# 2. SpringBoot管理静态资源

分析配置类 `WebMvcAutoConfiguration` ，得到不同位置的静态资源的优先级

```
优先级1:resources/resources
优先级2:resources/static
优先级3:resources/public

访问：localhost:80/res_name
```

yml 配置文件中修改静态资源目录（不建议改）

```
spring:
	mvc:
		static-path-pattern=
```

注：在 templates 目录下的所有页面，只能通过controller来跳转，且需要模板引擎的支持

# 3. 扩展SpringMVC

SpringBoot 提供了自动配置SpringMVC的功能，即 `WebMvcAutoConfiguration.java`。我们可以使用 JavaConfig，即用配置类手动接管这些配置并且扩展这些配置。一般在前后端不分离的项目中会用到。

实现手动配置，编写一个 `@Configuration`注解类，并且实现 `WebMvcConfigurer`接口


```java
@Configuration	// 表明这是一个配置类
public class MyMvcConfig implements WebMvcConfigurer {
    // 导入自定义的视图解析器
    @Bean
    public ViewResolver myView(){
        return new MyViewConfig();
    }
}
```