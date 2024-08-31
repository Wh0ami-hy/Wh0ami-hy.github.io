---
layout: post   	
catalog: true 	
tags:
    - SpringBoot
---

# 1. 什么是数据库连接池

数据库连接池就是在程序启动时就创建一定数量的数据库连接，将这些连接放入一个池子进行管理。由程序动态的进行连接的申请、使用和释放。注意，数据库不单单指Mysql，同样也可以为Redis设计连接池。
# 2. 连接池的运行原理

1. 从连接池获取连接或者创建连接；
2. 使用连接，用完归还到连接池；
3. 在系统关闭前，关闭所有连接并释放资源
# 3. 为什么要使用数据库连接池

- **资源复用**。避免了频繁的创建、销毁带来的性能开销，**减少系统资源消耗**的基础上，**增加了系统运行的稳定性**，主要**体现在减少内存碎片和线程或进程的临时创建**。
- **更快的响应速度**。由于程序启动时就准备好了若干连接备用，业务请求直接使用即可，不需要实时进行连接的创建、权限验证及销毁等操作，从而减少了系统的响应时间。
- **统一的连接管理，避免数据库连接泄漏**。可预先设定连接占用的超时时间，假如某条连接被占用超过设定值，可以强制回收该连接。

# 4. Mysql数据库连接的建立过程

1. 客户端发起连接请求，TCP三次握手
2. Mysql内部权限验证
3. SQL执行语句
4. Mysql关闭
5. 断开连接，TCP四次挥手

# 5. 整合Druid

Druid是高性能的关系型数据库连接池，它是阿里巴巴的一个开源项目。支持所有JDBC兼容的数据库，包括Oracle、MySQL、Derby、PostgreSQL、SQL Server、H2等。提供了丰富的监控和统计功能，可以帮助开发者更好地管理数据库连接

在pom.xml中导入依赖

```xml
<dependency>
	<groupId>com.alibaba</groupId>  
	<artifactId>druid-spring-boot-starter</artifactId>
    <version>1.1.5</version>
</dependency>
```

yml 文件中添加以下配置项，`com.alibaba.druid.pool.DruidDataSource` 基本配置参数

```
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/test?useSSL=false&serverTimezone=UTC
    username: root
    password: root
    driver-class-name: com.mysql.cj.jdbc.Driver
    # 指定使用 Druid 数据源
    type: com.alibaba.druid.pool.DruidDataSource
    # 配置 Druid 数据源相关属性
    druid:
      # 初始化时连接池的大小
      initial-size: 5
      # 连接池的最小空闲连接数
      min-idle: 5
      # 连接池的最大活跃连接数
      max-active: 20
      # 获取连接时最大等待时间
      max-wait: 60000
      # 用于检测空闲连接的 SQL 查询语句
      validation-query: SELECT 1
      # 是否在获取连接时验证连接
      test-on-borrow: true
      # 是否在归还连接时验证连接
      test-on-return: false
      # 是否在连接空闲时验证连接
      test-while-idle: true
      # 配置间隔多久进行一次检测，检测需要关闭的空闲连接，单位是毫秒
      time-between-eviction-runs-millis: 60000
      # 配置连接在池中最小生存的时间，单位是毫秒
      min-evictable-idle-time-millis: 300000
      # 配置连接在池中最大生存的时间，单位是毫秒
      max-evictable-idle-time-millis: 900000
      # 打开PSCache，并且指定每个连接上PSCache的大小
      pool-prepared-statements: true
      max-pool-prepared-statement-per-connection-size: 20
      # 配置监控统计拦截的filters，stat表示统计功能，wall表示防火墙
      filters: stat,wall,log4j
      # 合并多个DruidDataSource的监控数据
      use-global-data-source-stat: true
      # SQL执行时是否打印日志
      log-slow-sql: true
      # SQL执行超过多长时间就认为是慢SQL，单位毫秒
      slow-sql-millis: 2000
      # SQL日志是否需要格式化
      connection-properties: druid.stat.slowSqlMillis=2000;druid.stat.logSlowSql=true
      # 用于采集web-jdbc关联监控的数据
      webStatFilter:
        enabled: true
      # 提供监控信息展示的html页面
      statViewServlet:
        enabled: true
        # 设置白名单，不填则允许所有访问
        allow:
        url-pattern: /druid/*
        # 控制台管理用户名和密码
        login-username: admin
        login-password: admin
```

访问http://localhost:8080/druid/datasource.html
# 6. Druid 密码回调

很多项目都是把数据库的密码明文放在配置文件中，这样其实是不安全的，应该将密码加密后再放到配置中，这样可以一定程度的保护数据库密码的安全。

Druid 可以通过配置参数 passwordCallBack 来指定一个密码接口回调类进行密文密码解密操作。

**首先自定义一个密码接口回调类**

需要实现 DruidPasswordCallback 接口并重写接口方法 `setProperties()`

```java
import com.alibaba.druid.util.DruidPasswordCallback;
import lombok.extern.slf4j.Slf4j;
import org.apache.commons.lang3.StringUtils;
import java.util.Properties;

@Slf4j
public class DruidCustomPasswordCallback extends DruidPasswordCallback {

    @Override
    public void setProperties(Properties properties) {
        super.setProperties(properties);
        // 获取配置文件中的已经加密的密码（spring.datasource.druid.connect-properties.password）
        String pwd = (String)properties.get("password");
        if (StringUtils.isNotEmpty(pwd)) {
            try {
                // 这里的代码是将密码进行解密，并设置
                String password = "解密后的明文密码";
                setPassword(password.toCharArray());
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
}
```

**然后在yml 配置文件中配置密码接口回调类**

使用 `spring.datasource.druid.password-callback-class-name` 属性来配置密码接口回调类

根据密码接口回调类中的密文密码解密的逻辑，必须配置 `spring.datasource.druid.connect-properties.password` 属性才会进行密文密码解密操作并重置密码为解密后的明文密码
# 7. Druid 数据源监控

Druid 数据源具有监控的功能，并提供了一个 web 界面方便用户查看