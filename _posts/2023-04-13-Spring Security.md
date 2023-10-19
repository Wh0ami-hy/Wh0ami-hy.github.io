---
layout: post   	
catalog: true 	
tags:
    - Spring Security
---



# 1. 简介

在Spring Boot项目中使用Spring Security

[官网手册](https://docs.spring.io/spring-security/reference/5.7/servlet/getting-started.html)


FilterChainProxy

`FilterChainProxy` can be used to determine which `SecurityFilterChain` should be used.



利用AOP思想

Spring Security中重要的类：

1. WebSecurityConfigurerAdapter：自定义Security策略（自己编写配置类要继承该类）
2. AuthenticationManagerBuilder：自定义认证策略
3. @EnableWebSecurity：开启WebSecurity模式 （@Enablexxx 开启某个功能）

# 2. 导入依赖

在`pom.xml`中导入
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

## 2.1. 认证及授权

编写 WebSecurityConfig配置类

```java
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    // 授权：访问控制，哪些用户可以访问哪些页面，哪些不可以访问
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
                .antMatchers("/").permitAll()
                .antMatchers("/student/**").hasRole("student")
                .antMatchers("/teacher/**").hasRole("teacher")
                ;
       // 登录页设置
        http.formLogin();
    }
    // 认证：可以从内存中认证，可以从数据库中认证
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication()
                .withUser("hy").password("root").roles("student");
    }
}
```

## 2.2. 注销及权限控制

权限控制：即不同身份的人登陆，看到的页面是不一样的

```java
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        // 注销，logoutSuccessUrl 注销成功后跳转的页面
        http.logout().logoutSuccessUrl("/");
    }
}
```

## 2.3. 记住我功能的实现

本质是Cookie的保存

```java
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        // 记住我功能的实现
        http.rememberMe();
    }
}
```

## 2.4. Spring Security 常用的属性

下面列出了一些常用的属性：

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

Spring Security最大的用处在于为Web应用提供了全面的安全性保障，包括：

1. 身份验证：Spring Security提供了多种身份验证方式，包括基于表单、HTTP基本身份验证、OpenID认证、LDAP认证等，使得开发者可以轻松地实现用户身份验证功能。
2. 授权：Spring Security可以帮助开发者实现细粒度的授权控制，可以基于用户、角色、权限等多个维度进行授权控制，从而实现不同用户对应用程序资源的不同访问权限。
3. 防止攻击：Spring Security提供了多种安全措施，如防止跨站点请求伪造（CSRF）、防止会话固定攻击（Session Fixation）等，可以保护Web应用免受各种攻击。
4. 与Spring框架的整合：Spring Security与Spring框架天然集成，可以轻松地与Spring框架中的其他组件协同工作，如Spring MVC、Spring Boot等，从而实现全面的Web应用安全性保障



# 3. 身份验证（Authentication）

Spring Security 提供了多种身份验证机制，包括基于表单、基于HTTP基本认证、基于LDAP等

## 3.1. authenticate with a username/password

### 3.1.1. Reading Username/Password

**基于表单的身份认证**

验证form表单提供的数据

**基于http的身份认证（不推荐）**

通过在 HTTP 请求头中包含用户名和密码的 Base64 编码来进行身份验证。用户在每个请求中都需要提供凭据，以便进行身份验证

**基于Digest的身份验证（不推荐）**

### 3.1.2. Password Storage

**内存存储**

**关系型数据库存储**

**自定义存储**


## 3.2. Session Management
## 3.3. remember a user past session expiration

## 3.4. Handling Logouts

## 3.5. Authentication Events
# 4. 授权（Authorization）

Spring Security 提供了强大的授权机制，可以基于角色、权限或自定义逻辑来控制用户对资源的访问权限。您可以在配置文件或注解中定义授权规则，以限制用户对特定功能或页面的访问。

# 5. 安全配置（Security Configuration）

通过 Spring Security 的配置，您可以定义安全规则，如哪些URL路径需要保护、对于不同的用户角色如何进行授权、是否启用跨站请求伪造（CSRF）保护等。您可以使用 Java 配置或 XML 配置来定义这些规则。

## 5.1. Java Configuration

### 5.1.1. 创建类文件

创建一个Spring Security Java Configuration 类文件
```java
@EnableWebSecurity
public class WebSecurityConfig {

	@Bean
	public UserDetailsService userDetailsService() {
		InMemoryUserDetailsManager manager = new InMemoryUserDetailsManager();
		manager.createUser(User.withDefaultPasswordEncoder().username("user").password("password").roles("USER").build());
		return manager;
	}
}

```
AbstractSecurityWebApplicationInitializer 

```java
public class SecurityWebApplicationInitializer
	extends AbstractSecurityWebApplicationInitializer {

}
```

### 5.1.2. HttpSecurity


# 6. 认证和授权的集成

Spring Security 可以与其他常见的身份验证和授权机制进行集成，如LDAP、OAuth、OpenID Connect等。这使得在现代应用程序中实现单点登录（SSO）和第三方身份验证变得更加容易。

# 7. 安全事件和日志记录

Spring Security 提供了安全事件的记录和处理机制，您可以根据需要记录和处理登录成功、登录失败、访问受限等事件。这有助于监控应用程序的安全性和及时发现潜在的安全问题。