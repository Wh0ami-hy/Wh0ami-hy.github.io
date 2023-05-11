---
layout: post   	
catalog: true 	
tags:
    - Spring
---



# 1. Spring Security

官网：https://docs.spring.io/spring-security/site/docs/5.3.13.RELEASE/reference/html5/#jc

实现对Java应用程序的身份验证和授权（安全思想在规划网站时就要考虑好）

利用AOP思想

Spring Security中重要的类：

1. WebSecurityConfigurerAdapter：自定义Security策略（自己编写配置类要继承该类）
2. AuthenticationManagerBuilder：自定义认证策略
3. @EnableWebSecurity：开启WebSecurity模式 （@Enablexxx 开启某个功能）

导入依赖pom.xml

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

## 1.1. 认证及授权

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

## 1.2. 注销及权限控制

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

## 1.3. 记住我功能的实现

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

## 1.4. Spring Security 常用的属性

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