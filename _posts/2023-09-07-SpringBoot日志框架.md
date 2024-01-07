---
layout: post   	
catalog: true 	
tags:
    - Spring
---



# 1. 为什么要使用日志框架

使用 `System.out.println()`来打印信息耗费系统资源，不方便排查问题

# 2. Logback

SpringBoot默认的Logback，因为Logback是SpringBoot自带的，所以只要引入了SpringBoot就不需要单独引入Logback。

简单的配置可以在application.yml中直接配置。复杂的配置将Logback配置文件`logback.xml`或`logback-spring.xml`放在resource目录下，SpringBoot会自动加载并覆盖默认的配置。推荐优先使用带有`-spring`的文件名作为你的日志配置

也可以自定义配置文件名，在application.yml中指定`logging.config=classpath:logging-config.xml`


```xml
<configuration debug="false">  
  
    <!-- 定义日志文件的存储地址，不要在 LogBack 的配置中使用相对路径 -->  
    <property name="LOG_HOME" value="./log" />  
  
    <!-- 控制台日志，控制台输出 -->  
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">  
        <encoder>  
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS}-[%thread]-%highlight(%-5level) %cyan(%logger{50})-%msg%n</pattern>  
        </encoder>  
    </appender>  
  
    <!-- 文件日志，按照每天生成日志文件 -->  
    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">  
        <file>${LOG_HOME}/TestWeb.log</file>  
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">  
            <fileNamePattern>${LOG_HOME}/TestWeb.%d{yyyy-MM-dd}.log</fileNamePattern>  
            <maxHistory>30</maxHistory>  
        </rollingPolicy>  
        <encoder>  
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS}-[%thread]-%highlight(%-5level) %logger{50}-%msg%n</pattern>  
        </encoder>  
    </appender>  
  
    <!-- mybatis log configure -->  
    <logger name="com.apache.ibatis" level="TRACE" />  
  
    <!-- JDBC日志 -->  
    <logger name="java.sql.Connection" level="DEBUG" />  
    <logger name="java.sql.Statement" level="DEBUG" />  
    <logger name="java.sql.PreparedStatement" level="DEBUG" />  
  
    <!-- 日志输出级别 -->  
    <root level="DEBUG">  
        <appender-ref ref="STDOUT" />  
        <appender-ref ref="FILE" />  
    </root>  
  
</configuration>
```

# 3. Log4j2

如果对日志框架的性能有要求，则使用Log4j2，否则使用默认的Logback即可

注意：如果要使用Log4j2，需要在引入springboot时排除Logback

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-logging</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-log4j2</artifactId>
</dependency>
```

简单的配置可以在application.yml中直接配置。复杂的配置将Log4j2配置文件`log4j2.xml`或`log4j2-spring.xml`放在resource目录下，SpringBoot会自动加载并覆盖默认的配置。Log4j2还允许我们使用YAML或JSON配置。推荐优先使用带有`-spring`的文件名作为你的日志配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
    <Appenders>
        <Console name="Console" target="SYSTEM_OUT">
            <PatternLayout
                pattern="%style{%d{ISO8601}}{black} %highlight{%-5level }[%style{%t}{bright,blue}] %style{%C{1.}}{bright,yellow}: %msg%n%throwable" />
        </Console>

        <RollingFile name="RollingFile"
            fileName="./logs/spring-boot-logger-log4j2.log"
            filePattern="./logs/$${date:yyyy-MM}/spring-boot-logger-log4j2-%d{-dd-MMMM-yyyy}-%i.log.gz">
            <PatternLayout>
                <pattern>%d-%p-%C{1.}-[%t]-%m%n</pattern>
            </PatternLayout>
            <Policies>
                <!-- rollover on startup, daily and when the file reaches 
                    10 MegaBytes -->
                <OnStartupTriggeringPolicy />
                <SizeBasedTriggeringPolicy
                    size="10 MB" />
                <TimeBasedTriggeringPolicy />
            </Policies>
        </RollingFile>
    </Appenders>

    <Loggers>
        <!-- LOG everything at INFO level -->
        <Root level="info">
            <AppenderRef ref="Console" />
            <AppenderRef ref="RollingFile" />
        </Root>

        <!-- LOG "com.baeldung*" at TRACE level -->
        <Logger name="com.baeldung" level="trace"></Logger>
    </Loggers>

</Configuration>
```

# 4. SLF4J

**[SLF4J](http://www.slf4j.org/)  ，即简单日志门面（Simple Logging Facade for Java），不是具体的日志解决方案，它只服务于各种各样的日志系统。SLF4J是一个用于日志系统的日志抽象层，允许开发人员灵活的切换所需的日志框架，实现Log4j、Logback等多种日志框架的切换。将应用系统和具体的日志框架解耦合**

# 5. SLF4J搭配日志框架

- **slf4j + logback**： slf4j-api.jar + logback-classic.jar + logback-core.jar
- **slf4j + log4j**： slf4j-api.jar + slf4j-log4j2.jar + log4j.jar

SLF4J的唯一强制依赖项是slf4j-api

```xml
<dependency>
	<groupId>org.slf4j</groupId>
	<artifactId>slf4j-api</artifactId>
</dependency>
```

![slf4j-with-logging-frameworks](F:\笔记\博客\文章图片\slf4j-with-logging-frameworks.png)

## 5.1. SLF4J + Logback

在Spring Boot 的程序中使用SLF4J，不需要单独引入SLF4J，我们引入的`spring-boot-starter-web`中就已经包含了有关`slf4j`的jar包。因为Logback是SpringBoot自带的，所以只要引入了SpringBoot就不需要单独引入Logback

配合Lombok使用`@Slf4j`，使用时，在声明`@Slf4j`注解后，即可使用`log.info()`打印日志

在项目的`src/main/resources`目录下创建一个名为`logback.xml`的文件

## 5.2. SLF4J + Log4j2

在Spring Boot项目中使用SLF4J作为日志门面，并使用Log4j2作为日志实现。可以根据需要调整Log4j2的配置文件和日志级别配置来满足需求

在项目的`src/main/resources`目录下创建一个名为`log4j2.xml`的文件

# 6. 打印哪些日志

可以使用 `<logger>` 元素为特定的包或类定义日志记录规则，以控制它们的日志输出行为，`name` 属性值可以是一个包的名称，也可以是一个具体类的全限定名

```xml
<configuration>
    <!-- 其他配置项... -->

    <logger name="com.example.package1" level="DEBUG" />
    <logger name="com.example.package2" level="INFO" />

    <!-- 其他配置项... -->
</configuration>
```

对于 `com.example.package1`，我们将日志级别设置为 DEBUG，这意味着日志记录器将输出 DEBUG 及以上级别的日志。

**Myibatis log**

```xml
<!-- mybatis log configure -->  
<logger name="com.apache.ibatis" level="TRACE" />  
  
<!-- JDBC日志 -->  
<logger name="java.sql.Connection" level="DEBUG" />  
<logger name="java.sql.Statement" level="DEBUG" />  
<logger name="java.sql.PreparedStatement" level="DEBUG" />
```