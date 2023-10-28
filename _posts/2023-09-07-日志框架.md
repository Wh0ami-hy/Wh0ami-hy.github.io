---
layout: post   	
catalog: true 	
tags:
    - Spring
---

# 1. 为什么要使用日志框架

使用 `System.out.println()`来打印信息耗费系统资源

# 2. 有哪些日志框架

[Log4j2](https://logging.apache.org/log4j/)  、[Logback](http://logback.qos.ch/)  

注意：spring boot自带的日志框架是logback，如果要使用Log4j等其他日志框架，需要在引入springboot时排除logback
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
```

# 3. SLF4J

**[SLF4J](http://www.slf4j.org/)  ，即简单日志门面（Simple Logging Facade for Java），不是具体的日志解决方案，它只服务于各种各样的日志系统。按照官方的说法，SLF4J是一个用于日志系统的日志抽象层，允许开发人员在部署时选择所需的日志框架，与Log4j、Logback等多个日志实现框架无缝集成。实现将系统和具体的日志实现框架解耦合**


# 4. SLF4J搭配日志框架使用

- **slf4j + logback**： slf4j-api.jar + logback-classic.jar + logback-core.jar
- **slf4j + log4j**： slf4j-api.jar + slf4j-log4j2.jar + log4j.jar
- **可以只用slf4j无日志实现**：slf4j-api.jar + slf4j-nop.jar

# 5. SLF4J + Log4j2

在Spring Boot项目中使用SLF4J作为日志门面，并使用Log4j2作为日志实现。可以根据需要调整Log4j2的配置文件和日志级别配置来满足需求

引入依赖

```xml
<!-- SLF4J and Log4j2 -->
<dependency>
	<groupId>org.slf4j</groupId>
	<artifactId>slf4j-api</artifactId>
</dependency>
<dependency>
	<groupId>org.apache.logging.log4j</groupId>
	<artifactId>log4j-slf4j-impl</artifactId>
</dependency>
<dependency>
	<groupId>org.apache.logging.log4j</groupId>
	<artifactId>log4j-core</artifactId>
</dependency>
```

在项目的`src/main/resources`目录下创建一个名为`log4j2.xml`的文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN">
    <Appenders>
        <Console name="LogToConsole" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
    </Appenders>
    <Loggers>
        <Logger name="com.malaoshi" level="debug" additivity="false">
            <AppenderRef ref="LogToConsole"/>
        </Logger>
        <Root level="error">
            <AppenderRef ref="LogToConsole"/>
        </Root>
    </Loggers>
</Configuration>
```

在Spring Boot的配置文件`application.yml`中配置日志级别

```xml
# Log4j2 Log Level
logging.level.root=INFO
```

代码中使用

```java
import org.slf4j.Logger;  
import org.slf4j.LoggerFactory;

public class className {
	private static final Logger LOGGER = LoggerFactory.getLogger(className.class);
	...
	...
	LOGGER.info("logInfo");
}
```

# 6. SLF4J + logback

引入依赖，因为logback是springboot自带的，所以只要引入了springboot就不需要单独引入logback

```xml

    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-api</artifactId>
    </dependency>
```
在项目的`src/main/resources`目录下创建一个名为`logback.xml`的文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="false">

    <!--定义日志文件的存储地址 勿在 LogBack 的配置中使用相对路径-->
    <property name="LOG_HOME" value="/home" />

    <!--控制台日志，控制台输出 -->
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度,%msg：日志消息，%n是换行符-->
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
        </encoder>
    </appender>

    <!--文件日志，按照每天生成日志文件 -->
    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--日志文件输出的文件名-->
            <FileNamePattern>${LOG_HOME}/TestWeb.log.%d{yyyy-MM-dd}.log</FileNamePattern>
            <!--日志文件保留天数-->
            <MaxHistory>30</MaxHistory>
        </rollingPolicy>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符-->
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
        </encoder>
        <!--日志文件最大的大小-->
        <triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">
            <MaxFileSize>10MB</MaxFileSize>
        </triggeringPolicy>
    </appender>

    <!-- show parameters for hibernate sql 专为 Hibernate 定制 -->
    <logger name="org.hibernate.type.descriptor.sql.BasicBinder" level="TRACE" />
    <logger name="org.hibernate.type.descriptor.sql.BasicExtractor" level="DEBUG" />
    <logger name="org.hibernate.SQL" level="DEBUG" />
    <logger name="org.hibernate.engine.QueryParameters" level="DEBUG" />
    <logger name="org.hibernate.engine.query.HQLQueryPlan" level="DEBUG" />

    <!--myibatis log configure-->
    <logger name="com.apache.ibatis" level="TRACE"/>
    <logger name="java.sql.Connection" level="DEBUG"/>
    <logger name="java.sql.Statement" level="DEBUG"/>
    <logger name="java.sql.PreparedStatement" level="DEBUG"/>

    <!-- 日志输出级别 -->
    <root level="DEBUG">
        <appender-ref ref="STDOUT" />
        <appender-ref ref="FILE"/>
    </root>
</configuration>
```

# 7. 参考范例

`log4j2.xml`的参考范例，直接放到项目里面改一下包名就可以用

**输出日志到Console**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="DEBUG">
    <Appenders>
        <Console name="LogToConsole" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
    </Appenders>
    <Loggers>
        <!-- avoid duplicated logs with additivity=false -->
        <Logger name="com.mkyong" level="debug" additivity="false">
            <AppenderRef ref="LogToConsole"/>
        </Logger>
        <Root level="error">
            <AppenderRef ref="LogToConsole"/>
        </Root>
    </Loggers>
</Configuration>
```


**输出日志到文件**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="DEBUG">
    <Appenders>
        <Console name="LogToConsole" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
        <File name="LogToFile" fileName="logs/app.log">
            <PatternLayout>
                <Pattern>%d %p %c{1.} [%t] %m%n</Pattern>
            </PatternLayout>
        </File>
    </Appenders>
    <Loggers>
        <Logger name="com.malaoshi" level="debug" additivity="false">
            <AppenderRef ref="LogToFile"/>
            <AppenderRef ref="LogToConsole"/>
        </Logger>
        <Logger name="org.springframework.boot" level="error" additivity="false">
            <AppenderRef ref="LogToConsole"/>
        </Logger>
        <Root level="error">
            <AppenderRef ref="LogToFile"/>
            <AppenderRef ref="LogToConsole"/>
        </Root>
    </Loggers>
</Configuration>
```

**输出到文件并滚动生成新的日志文件**

```xml
<Configuration status="DEBUG">
    <Appenders>
        <Console name="LogToConsole" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
        <RollingFile name="LogToRollingFile" fileName="logs/app.log"
                    filePattern="logs/$${date:yyyy-MM}/app-%d{MM-dd-yyyy}-%i.log.gz">
            <PatternLayout>
                <Pattern>%d %p %c{1.} [%t] %m%n</Pattern>
            </PatternLayout>
            <Policies>
                <TimeBasedTriggeringPolicy />
                <SizeBasedTriggeringPolicy size="10 MB"/>
            </Policies>
        </RollingFile>
    </Appenders>

    <Loggers>
        <!-- avoid duplicated logs with additivity=false -->
        <Logger name="com.malaoshi" level="debug" additivity="false">
            <AppenderRef ref="LogToRollingFile"/>
        </Logger>
        <Root level="error">
            <AppenderRef ref="LogToConsole"/>
        </Root>
    </Loggers>
</Configuration>
```