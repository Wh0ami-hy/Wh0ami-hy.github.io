---
layout: post   	
catalog: true 	
tags:
    - Spring Security
---



# 1. Spring Security 项目搭建

[官网手册](https://docs.spring.io/spring-security/reference/5.7/servlet/getting-started.html)

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>    
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

导入spring-boot-starter-security启动器后，Spring Security已经生效，默认拦截全部请求，在浏览器访问项目地址会进入Spring Security内置登录页面

- 用户名： user
- 密码：项目启动后，打印在控制台中
# 2. Spring Security 配置

可以使用 Java 配置或 XML 配置

在Spring Boot 2.7.0 之前的版本中，我们需要写个配置类继承`WebSecurityConfigurerAdapter`，然后重写Adapter中的三个方法进行配置

```java
@Configuration
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true)  //方法级的权限认证
public class OldSecurityConfig extends WebSecurityConfigurerAdapter {
    @Autowired
    private UmsAdminService adminService;

    @Override
    protected void configure(HttpSecurity httpSecurity) throws Exception {
        //省略HttpSecurity的配置
    }

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.userDetailsService(userDetailsService())
                .passwordEncoder(passwordEncoder());
    }
    
    @Bean
    @Override
    public AuthenticationManager authenticationManagerBean() throws Exception {
        return super.authenticationManagerBean();
    }
}
```

新版本中，如果以后想要配置过滤器链，可以通过自定义`SecurityFilterChain` Bean来实现。如果以后想要配置WebSecurity，可以通过`WebSecurityCustomizer` Bean来实现。


**Spring Security中重要的类**

- WebSecurityConfigurerAdapter：自定义Security策略（自己编写配置类要继承该类）
- AuthenticationManagerBuilder：自定义认证策略
- @EnableWebSecurity：开启WebSecurity模式 （@Enablexxx 开启某个功能）
# 3. 身份认证（Authentication）

Spring Security 提供了多种身份验证机制，包括基于表单、基于HTTP基本认证、基于LDAP等

下面主要介绍基于表单的认证

## 3.1. 用户名密码认证

在实际项目中，认证逻辑是需要自定义控制的。将 UserDetailsService 接口的实现类用`@Bean`放入Spring容器即可自定义认证逻辑

UserDetailsService 的实现类必须重写 loadUserByUsername 方法，该方法定义了具体的认证逻辑，参数 username 是前端传来的用户名，我们需要根据传来的用户名查询到该用户（一般是从数据库查询），并将查询到的用户封装成一个UserDetails对象，该对象是Spring Security提供的用户对象，包含用户名、密码、权限。Spring Security会根据 UserDetails对象中的密码和客户端提供密码进行比较。相同则认证 通过，不相同则认证失败。
### 3.1.1. 内存认证

InMemoryUserDetailsManager 是 UserDetailsService 接口的一个实现类，它将登录页传来的用户名密码和内存中用户名密码做匹配认证

### 3.1.2. 数据库认证

Spring Security 默认从内存加载用户，需要实现从数据库加载并校验用户。

- 创建 **UserServiceImpl** 类
- 实现 **UserDetailsService** 接口
- 重写 **loadUserByUsername** 方法
- 根据用户名校验用户并查询用户相关权限信息（授权）
- 将数据封装成 **UserDetails**（创建类并实现该接口） 并返回

### 3.1.3. 密码解析器（PasswordEncoder）

Spring Security要求容器中必须有 PasswordEncoder 实例，之前使用的NoOpPasswordEncoder 是 PasswordEncoder 的实现类，意思是不解析密码，使用明文密码。Spring Security官方推荐的密码解析器是 BCryptPasswordEncoder。在开发中，我们将 BCryptPasswordEncoder 的实例放入Spring容器即可，并 且在用户注册完成后，将密码加密再保存到数据库
### 3.1.4. 自定义登录页面

Spring Security 默认提供了登录页面，但在实际项目中是使用自己的登录页面。Spring Security也支持用户自定义登录页面

```java
@Configuration  
@EnableWebSecurity  
public class SecurityConfig {
    @Bean  
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception{  
        http  
                // 过滤请求  
                .authorizeRequests()  
                // 接口放行  
                .antMatchers("/user/login").permitAll()  
                .anyRequest()  
                .authenticated()  
                .and()  
                .csrf().disable()  
                .headers().cacheControl().disable()  
                .and()  
                .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS);  
      
        return http.build();  
    }
}
```

Spring Security 为了防止CSRF攻击，默认开启了CSRF防护，这限制了除了 GET请求以外的大多数方法。我们要想正常使用Spring Security需要突破CSRF防护

**解决方法一：关闭CSRF防护：**

`http.csrf().disable();`
## 3.2. 认证后的处理

认证后，如果除了跳转页面还需要执行一些自定义代码时， 如：统计访问量，推送消息等操作时，可以自定义处理器
### 3.2.1. 认证成功后的处理方式



### 3.2.2. 认证失败后的处理方式


## 3.3. 会话管理

Security将用户信息保存在会话中，并提供会话管理，我们可以从 SecurityContext 对象中获取用户信息， SecurityContext 对象与当前线程进行绑定

SecurityContextHolder工具类就是把SecurityContext存储在当前线程中。

SecurityContextHolder可以用来设置和获取SecurityContext。它主要是给框架内部使用的，可以利用它获取当前用户的SecurityContext进行请求检查，和访问控制等。

在Web环境下，SecurityContextHolder是利用ThreadLocal来存储SecurityContext的。

```java
@CrossOrigin  
@RestController  
@RequestMapping("/user")  
public class UserController {  
    @PostMapping("/login")  
    public String login(@RequestBody User user){  
  
        SecurityContext context = SecurityContextHolder.getContext();  
        System.out.println(context.toString());  
        return "hello";  
    }  
}
```


## 3.4. remember me 功能

`记住我`功能，即下次访问系统时无需重新登录。当使用`记住我`功能登录后，Spring Security会生成一个令牌，令牌一方面保存到数据库中，另一方面生成一个叫 `remember-me` 的Cookie保存到客户端。之后客户端访问项目时自动携带令牌，不登录即可完成认证。

## 3.5. 退出登录

在系统中一般都有退出登录的操作。退出登录后，Spring Security 进行了以下操作：

- 清除认证状态
- 销毁HttpSession对象 
- 跳转到登录页面
## 3.6. 退出成功处理器


# 4. 授权（Authorization）

Spring Security 提供了强大的授权机制，可以基于角色、权限或自定义逻辑来控制用户对资源的访问权限。您可以在配置文件或注解中定义授权规则，以限制用户对特定功能或页面的访问。

## 4.1. 封装UserDetails对象

- 查询用户
- 查询用户权限
- 将自定义权限集合转为Security的权限类型集合
- 封装为UserDetails对象
- 返回封装好的UserDetails对象


## 4.2. 资源访问控制

### 4.2.1. 配置类设置

### 4.2.2. 自定义访问控制逻辑

如果资源数量很多，一条条配置需要的权限效率较低。我们可以自定义访问控制逻辑，即访问资源时判断用户是否具有名为该资源 URL的权限。

### 4.2.3. 注解设置访问控制

## 4.3. 无权限403处理方案

- 编写权限不足页面 noPermission.html
- 编写权限不足处理类
- 在Spring Security配置文件中配置异常处理



# 5. 认证和授权的集成

Spring Security 可以与其他常见的身份验证和授权机制进行集成，如LDAP、OAuth、OpenID Connect等。这使得在现代应用程序中实现单点登录（SSO）和第三方身份验证变得更加容易。

# 6. 安全事件和日志记录

Spring Security 提供了安全事件的记录和处理机制，您可以根据需要记录和处理登录成功、登录失败、访问受限等事件。这有助于监控应用程序的安全性和及时发现潜在的安全问题。


# 7. Spring Security 中重要对象汇总

# 8. Spring Security 常用的属性

1. `security.basic.enabled`：设置是否启用HTTP基本认证，默认为`true`。
2. `security.basic.realm`：设置HTTP基本认证的领域（realm）名称，默认为`Spring`。
3. `security.enable-csrf`：设置是否启用跨站请求伪造（CSRF）保护，默认为`true`。
4. `security.csrf.method`：设置CSRF保护使用的HTTP方法，默认为`POST`。
5. `security.ignored`：设置忽略安全性保护的URL模式，默认为空。
6. `security.headers.cache`：设置是否启用HTTP缓存头部，默认为`false`。
7. `security.headers.hsts`：设置是否启用HTTP严格传输安全性（HSTS）头部，默认为`false`。
8. `security.headers.frame`：设置是否允许在页面中显示嵌入式框架，默认为`DENY`。
9. `security.headers.xss`：设置是否启用跨站脚本（XSS）保护，默认为`true`。
10. `security.session.maximumSessions`：设置最大会话数，默认为无限制。
11. `security.session.maxSessionsPreventsLogin`：设置是否阻止用户登录，如果超出了最大会话数，默认为`false`。
12. `security.session.session-fixation-protection`：设置会话固定保护，默认为`none`。