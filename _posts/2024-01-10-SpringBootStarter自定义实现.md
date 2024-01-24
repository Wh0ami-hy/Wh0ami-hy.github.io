---
layout: post   	
catalog: true 	
tags:
    - SpringBoot
---



# 1. 基本步骤

- 创建一个starter项目，关于项目的命名，Spring 官方 Starter通常命名为`spring-boot-starter-{name}`如 spring-boot-starter-web， Spring官方建议非官方Starter命名应遵循`{name}-spring-boot-starter`的格式
- 创建一个ConfigurationProperties类用于保存你的配置信息（如果你的项目不使用配置信息则可以跳过这一步）
- 创建一个AutoConfiguration类，引用定义好的配置信息。在AutoConfiguration中实现所有starter应该完成的操作，并且把这个类加入spring.factories配置文件中进行声明
- 打包项目，之后在一个SpringBoot项目中引入该项目依赖，然后就可以使用该starter了

# 2. 创建一个SpringBoot项目

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

# 3. 创建属性类（可选）

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

# 4. 创建自动配置类

在应用程序启动时，如果类路径中存在User类，则MyAutoConfiguration类将运行。如果运行成功，它将通过MyProperties类读取属性，用user bean填充Spring应用程序上下文

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

# 5. 创建spring.factories

在`src/main/resources/META-INF`目录中添加一个`spring.factories`文件，将AutoConfiguration类在该文件中进行声明

```yml
org.springframework.boot.autoconfigure.EnableAutoConfiguration=com.hy.MyAutoConfiguration
```

# 6. 打包Starter

在项目的根目录中运行以下命令进行打包

```
mvn clean install
```

# 7. 在其他引入

```xml
<dependency>  
    <groupId>com.hy</groupId>  
    <artifactId>demo-spring-boot-starter</artifactId>  
    <version>0.0.1-SNAPSHOT</version>  
</dependency>
```