---
layout: post   	
catalog: true 	
tags:
    - SpringBoot
---



# 1. 基本步骤

- 创建一个starter项目，关于项目的命名，Spring 官方 Starter通常命名为`spring-boot-starter-{name}`如 spring-boot-starter-web， Spring官方建议非官方Starter命名应遵循`{name}-spring-boot-starter`的格式
- 一个/多个自定义配置的属性配置类（ConfigurationProperties类）如果你的项目不使用配置信息则可以跳过这一步
- 一个/多个自动配置类（AutoConfiguration类），引用定义好的配置信息。在AutoConfiguration中实现starter应该完成的所有操作
- 把自动配置类（AutoConfiguration类）写入到 Spring Boot 的 SPI 机制配置文件：spring.factories
- 打包项目，之后在一个SpringBoot项目中引入该项目依赖，然后就可以使用该starter了

# 2. Java SPI 机制简介

SPI 全称 Service Provider Interface，是 Java 提供的一套用来被第三方实现或者扩展的 API，它可以用来启用框架扩展和替换组件。

SPI 的大概流程是：调用方 –> 标准服务接口 –> 本地服务发现（配置文件） –> 具体实现

所以 Java SPI 实际上是**基于接口的编程＋策略模式＋配置文件组合实现的动态加载机制**

一个 SPI 的典型案例就是 JDBC 的驱动，Java JDBC 定义接口规范（java.sql.Driver），各个数据库厂商（MySQL/Oracle/MS SQLServer 等）去完成具体的实现，然后通过 SPI 配置文件引入具体的实现类

**一个简单的 Java SPI 开发步骤**

-   定义一个业务接口
-   编写接口实现类
-   创建 SPI 的配置文件，实现类路径写入配置文件中
-   通过 Java SPI 机制调用

step1

```java
public interface Robot {
	void sayHello();
}
```

step2

```java
public class Bumblebee implements Robot {
	@Override
	public void sayHello(){
		System.out.println("Hello");
	}
}
```

step3：在`META-INF/services` 文件夹下创建一个文件，名称为Robot的全路径类名 `ora.apache.spi.Robot`，内容为实现类的全路径类名`org.apache.spi.Bumblebee`

step4

```java
public class JavaSPITest {
	@Test
	public void sayHello() throws Exception {
		ServiceLoader<Robot> serviceLoader = ServiceLoader.load(Robot.class);
		System.out.println("java spi");
		serviceLoader.forEach(Robot::sayHello);
	}
}
```

# 3. Spring Boot SPI 机制实现

 Spring Boot 的 SPI 实现了，基本流程是一样的：读取配置文件 –> 将具体的实现类装配到 Spring Boot 的上下文环境中

# 4. 开发一个自定义 starter

## 4.1. 创建一个SpringBoot项目

项目的命名应遵循`{name}-spring-boot-starter`的格式

先在pom.xml中引入起始的依赖项（spring-boot-starter是必须的）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.lance.demo</groupId>
    <artifactId>my-spring-boot-starter</artifactId>
    <version>1.0.0-SNAPSHOT</version>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter</artifactId>
        <version>2.6.5</version>
    </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-source-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

## 4.2. 创建属性配置类（可选）

创建User类

```java
public class User {  
    private String username;  
    private String password;
    /**
    构造器
    getters and setters
    **/
    }
```

一个用于自定义配置的属性类。可以使用`@ConfigurationProperties`注解定义一组属性，并提供它们的getter和setter方法，以便在应用程序中进行配置

```java
@ConfigurationProperties(prefix = "my.user")
public class MyProperties {

    private String userName = "admin"; // 默认值
	private String password = "123";
    /** 
    getters and setters
	**/
}
```

## 4.3. 创建自动配置类

在应用程序启动时，如果类路径中存在User类，则MyAutoConfiguration类将运行

```java
@Configuration
@ConditionalOnClass(User.class)
@EnableConfigurationProperties(MyProperties.class)
public class MyAutoConfiguration {  

	@Autowired  
	MyProperties myProperties;
  
    private static final Logger logger = LoggerFactory.getLogger(MyAutoConfiguration.class);  
  
    public MyAutoConfiguration() {  
    }  
    @Bean  
    @ConditionalOnMissingBean // 它会保证你的bean只有一个，即你的实例只有一个
    public User userManager() {  
        logger.info("User bean start to create.");  
        User user = new User(myProperties.getUserName(),myProperties.getPassword());
        logger.info("user\t"+ user.toString());
        return user;  
    }}
```

运行之后，可在`application.properties` 中，修改属性值

```yml
my.user.userName=root  
my.user.password=root
```

## 4.4. 创建spring.factories

在`src/main/resources/META-INF`目录中添加一个`spring.factories`文件，将AutoConfiguration类在该文件中进行声明

```yml
org.springframework.boot.autoconfigure.EnableAutoConfiguration=com.hy.MyAutoConfiguration
```

## 4.5. 打包Starter

在项目的根目录中运行以下命令进行打包

```
mvn clean install
```

## 4.6. 自定义 starter 优化

属性配置自动提示功能：使用 Spring Boot 官方提供的 starter 的时候，在 application.properties 中编写属性配置是有自动提示功能的，要实现这个也很简单，引入一下依赖即可，该插件引入后，打包时会检查 @ConfigurationProperties 下的类，自动生成 spring-configuration-metadata.json 文件用于编写属性提示：

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-configuration-processor</artifactId>
  <optional>true</optional>
</dependency>
```

启动优化：前面有提到 Spring Boot 的 SPI 加载流程，会先加载自动配置元数据配置文件，引入以下依赖，该插件会自动生成 META-INF/spring-autoconfigure-metadata.properties，供 AutoConfigurationImportSelector 过滤加载，提升启动性能：

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-configuration-processor</artifactId>
  <optional>true</optional>
</dependency>
```

## 4.7. 在其他项目引入

```xml
<dependency>  
    <groupId>com.hy</groupId>  
    <artifactId>demo-spring-boot-starter</artifactId>  
    <version>0.0.1-SNAPSHOT</version>  
</dependency>
```