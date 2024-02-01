---
layout: post   	
catalog: true 	
tags:
    - SpringBoot
---


# 1. SpringBoot常用注解（重点）

**配置相关的**

- @SpringBootApplication：这是Spring Boot应用的主注解，它包含了@ComponentScan、@EnableAutoConfiguration和@Configuration三个注解，用于开启组件扫描、自动配置和配置类扫描等功能。
- @EnableAutoConfiguration：这个注解用于自动配置Spring应用，从classpath下的`META-INF/spring.factories`中读取自动配置类，根据配置自动装配Bean。
- @Configuration：这个注解用于标记一个配置类，表示该类包含了一些配置信息，可以用@Bean注解定义一些Bean。
- @ConditionalOnProperty：这个注解用于根据配置文件中的属性值来判断是否需要启用某个配置。

**请求相关的**

- @RestController：这个注解用于标记一个Controller类，它包含了@Controller和@ResponseBody注解的功能，用于处理HTTP请求并返回JSON、XML等格式的响应数据
- @RequestMapping：这个注解用于标记一个请求处理方法，指定处理的URL路径和HTTP请求方法。
- @GetMapping、@PostMapping、@PutMapping、@DeleteMapping：这些注解是对@RequestMapping注解的补充，分别表示处理GET、POST、PUT、DELETE请求的方法。
- @PathVariable：这个注解用于将URL路径中的变量映射到方法参数上。
- @RequestParam：这个注解用于将HTTP请求参数映射到方法参数上。
- @RequestBody：这个注解用于将HTTP请求体中的数据映射到方法参数上。
- @ResponseBody：表示方法的返回值将直接作为响应的内容返回给客户端，不会经过视图解析器进行视图渲染。可以将@ResponseBody注解应用在控制器类的方法上，也可以将其应用在方法的参数上

**注入相关的**

- @Bean：将对应的实例对象放进IOC容器
- @Autowired：自动装配一个IOC容器中存在的Bean
- @Resource：
- @Component：这个注解用于标记一个组件，表示该组件可以被Spring容器扫描并管理。
- @Service
- @Mapper
- @Repository

# 2. `@Controller`&`@Service`&`@Component`

`@Controller`和`@Service`都派生于`@Component`，都是Spring的注解

`@Component`用来标记所有被Spring容器管理的组件，本质上都是把实例化对象交给Spring管理

在平时的开发中，我们通常在控制层采用注解`@Controller`，在业务层采用注解`@Service`

Spring在启动时，有一个非常核心的类`ConfigurationClassPostProcessor`会对类路径下的所有类进行扫描，将符合条件的bean扫描出来添加到`beanDefinitionMap`集合中，方便接下来的实例化。

如果不使用SpringMVC时，三者使用其实是没有什么差别的，但如果使用了SpringMVC，`@Controller`就被赋予了特殊的含义

Spring会遍历上面扫描出来的所有bean，过滤出那些添加了注解`@Controller`的bean，将Controller中所有添加了注解`@RequestMapping`的方法解析出来封装成`RequestMappingInfo`存储到`RequestMappingHandlerMapping`中的`mappingRegistry`。后续请求到达时，会从`mappingRegistry`中查找能够处理该请求的方法

# 3. `@Repository`&`@Mapper`

`@Repository`是属于Spring的注解，`@Mapper`是属于Mybatis的注解

`@Repository`是`@Component`的一个派生品

**@Repository和@Mapper的异同**

在程序中，Mybatis需要找到对应的mapper，在编译时候动态生成代理类，实现数据库查询功能  
`@Mapper`和`@Repository`注解的使用方式一样，都是在持久层的接口上添加注解。但是如果只是单独的使用`@Mapper`注解的话，在idea中进行自动装配的时候，会出现警告，提示找不到这个bean。但是这个不影响程序运行，可以直接忽略。  想要不出现这个警告，可以在idea设置中对这种警告进行忽略，也可以在使用`@Mapper`的地方同时使用  `@Repository`注解。这样Spring会扫描`@Repository`并识别这个bean，就不会出现这个警告

`@Repository` + 在SpringBoot的启动类上使用`@MapperScan`注解 = `@Mapper`

```java
@MapperScan(value = {"com.bf.spring4.mapper"})
```

# 4. `@Autowired`&`@Bean`&`@Resource`

`@Bean`修饰的只有方法，并且功能单一，就是初始化一个bean然后交给IOC容器管理。

`@Autowired`可以修饰常量和方法，用于自动装配（自动装配的前提你要先有才能装配，你直接引用资源就行，没有相当于白扯）

`@Resource`可以修饰常量和方法，用于自动装配（自动装配的前提你要先有才能装配，你直接引用资源就行，没有相当于白扯）

`@Autowired` 和 `@Resource` 的区别主要体现在以下 5 点：

1.  来源不同；
2.  依赖查找的顺序不同；
3.  支持的参数不同；
4.  依赖注入的用法不同；
