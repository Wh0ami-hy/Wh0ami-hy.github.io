---
layout: post   	
catalog: true 	
tags:
    - Spring
---


# 1. 创建一个自定义Starter

要创建自定义启动器，我们需要编写以下组件
- 一个用于自定义库的自动配置类以及一个用于自定义配置的属性类
- 引入库和自动配置项目的依赖项的起始pom

下面开始实现一个`greeter-spring-boot-starter`

_greeter-spring-boot-autoconfigure_

## 1.1. 自动配置模块

我们将调用自动配置模块`greeter-spring-boot-autoconfigure`

这个模块有两个主要类
- GreeterProperties，它允许通过属性文件`application.properties`设置自定义属性
- GreeterAutoConfiguartion，将为greeter库创建bean

GreeterProperties
```java
@ConfigurationProperties(prefix = "baeldung.greeter")
public class GreeterProperties {

    private String userName;
    private String morningMessage;
    private String afternoonMessage;
    private String eveningMessage;
    private String nightMessage;
    // standard getters and setters
}
```

GreeterAutoConfiguartion

```java
@Configuration
@ConditionalOnClass(Greeter.class)
@EnableConfigurationProperties(GreeterProperties.class)
public class GreeterAutoConfiguration {

    @Autowired
    private GreeterProperties greeterProperties;

    @Bean
    @ConditionalOnMissingBean
    public GreetingConfig greeterConfig() {

        String userName = greeterProperties.getUserName() == null
          ? System.getProperty("user.name") 
          : greeterProperties.getUserName();
        
        // ..

        GreetingConfig greetingConfig = new GreetingConfig();
        greetingConfig.put(USER_NAME, userName);
        // ...
        return greetingConfig;
    }

    @Bean
    @ConditionalOnMissingBean
    public Greeter greeter(GreetingConfig greetingConfig) {
        return new Greeter(greetingConfig);
    }
}
```

我们还需要在`src/main/resources/meta-inf`目录中添加一个spring.factories文件

```xml
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
  com.baeldung.greeter.autoconfigure.GreeterAutoConfiguration
```

在应用程序启动时，如果类路径中存在类Greeter，则GreeterAutoConfiguration类将运行。如果运行成功，它将通过GreeterProperties类读取属性，用GreeterConfig和Greeter bean填充Spring应用程序上下文。

## 1.2. 创建 pom.xml

创建starter pom，它将引入自动配置模块和greeter库的依赖项。

根据命名约定，所有不由核心Spring Boot团队管理的启动程序都应该以库名开头，`后缀-spring-boot-starter`。所以将我们的启动器称为`greeter-spring-boot-starter`

```xml
<project ...>
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.baeldung</groupId>
    <artifactId>greeter-spring-boot-starter</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <greeter.version>0.0.1-SNAPSHOT</greeter.version>
        <spring-boot.version>2.2.6.RELEASE</spring-boot.version>
    </properties>

    <dependencies>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
            <version>${spring-boot.version}</version>
        </dependency>

        <dependency>
            <groupId>com.baeldung</groupId>
            <artifactId>greeter-spring-boot-autoconfigure</artifactId>
            <version>${project.version}</version>
        </dependency>

        <dependency>
            <groupId>com.baeldung</groupId>
            <artifactId>greeter</artifactId>
            <version>${greeter.version}</version>
        </dependency>

    </dependencies>

</project>
```

## 1.3. 使用Starter

```xml
<dependency>
    <groupId>com.baeldung</groupId>
    <artifactId>greeter-spring-boot-starter</artifactId>
    <version>${greeter-starter.version}</version>
</dependency>
```

Spring Boot will automatically configure everything and we will have a _Greeter_ bean ready to be injected and used.

Let’s also change some of the default values of the _GreeterProperties_ by defining them in the _application.properties_ file with the _baeldung.greeter_ prefix

```xml
baeldung.greeter.userName=Baeldung
baeldung.greeter.afternoonMessage=Afternoon
```

Finally, let’s use the _Greeter_ bean in our application




要实现一个Spring Boot Starter，可以按照以下步骤进行：

创建一个Maven或者Gradle项目。

添加Spring Boot Starter的依赖，通常可以使用spring-boot-starter-parent作为父项目，以便继承Spring Boot的默认配置和依赖管理。

实现自动配置逻辑。可以通过编写一个自动配置类来实现，该类通常使用@Configuration注解进行标记，同时使用@EnableAutoConfiguration注解启用自动配置。

定义配置属性。可以使用@ConfigurationProperties注解定义一组属性，并提供它们的getter和setter方法，以便在应用程序中进行配置。

编写必要的代码和逻辑。根据自定义Starter的功能，编写必要的代码和逻辑，例如定义Spring组件、提供服务等。

打包和发布Starter。将项目打包为可供其他应用程序使用的jar文件，并将其发布到Maven仓库或本地仓库。



如：实现自定义线程池

手动实现一个 Spring Boot 启动器（Starter），需要完成以下步骤：

**创建一个 Maven 项目**

用于存储定制的 Starter

**添加依赖**

在项目的 pom.xml 文件中添加以下依赖：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-autoconfigure</artifactId>
    <version>2.6.3</version>
</dependency>
```

这个依赖包含了 Spring Boot 的自动配置模块，它允许你通过 Java 代码来自定义 Spring Boot 的自动配置

**创建自动配置类**

创建一个自动配置类，用于自定义 Spring Boot 的自动配置。这个类应该包含一个 `@Configuration` 注解和一个 `@ConditionalOnClass` 注解，用于指定自动配置类的条件，例如：

```java
@Configuration
@ConditionalOnClass(MyService.class)
public class MyAutoConfiguration {
    @Bean
    @ConditionalOnMissingBean
    public MyService myService() {
        return new MyService();
    }
}
```

这个自动配置类会在项目中存在 `MyService` 类时自动生效，它会创建一个名为 `myService` 的 Bean 并将它添加到 Spring 应用程序上下文中

**创建 Starter 类**

创建一个 Starter 类，用于提供自动配置类和其他必要的依赖。这个类应该包含一个 `@Configuration` 注解和一个 `@EnableConfigurationProperties` 注解，用于启用自动配置和配置属性的支持，例如：

```java
@Configuration
@EnableConfigurationProperties(MyProperties.class)
@AutoConfigureAfter(MyAutoConfiguration.class)
public class MyStarterAutoConfiguration {
    @Autowired
    private MyProperties properties;

    @Bean
    public MyService myService() {
        return new MyService(properties.getGreeting());
    }
}
```

这个 Starter 类会自动启用 `MyAutoConfiguration` 自动配置类，并创建一个名为 `myService` 的 Bean，它使用 `MyProperties` 配置类中的属性来初始化 `MyService` 类的实例

**创建配置属性类**

创建一个配置属性类，用于定义 Starter 的配置属性。这个类应该包含一个 `@ConfigurationProperties` 注解，用于指定配置属性的前缀和默认值，例如：

```java
@ConfigurationProperties("my.starter")
public class MyProperties {
    private String greeting = "Hello";

    public String getGreeting() {
        return greeting;
    }

    public void setGreeting(String greeting) {
        this.greeting = greeting;
    }
}
```

这个配置属性类定义了一个名为 `greeting` 的属性，它的默认值是 "Hello"。这个属性可以在 Starter 类中使用，用于初始化 `MyService` 类的实例。

**打包和安装 Starter**

在项目的根目录中运行以下命令，将 Starter 打包并安装到本地 Maven 仓库中：

```
mvn clean install
```

现在你已经手动实现了一个 Spring Boot Starter。要在其他项目中使用它，只需在项目的 pom.xml 文件中添加以下依赖：

```xml
<dependency>
    <groupId>com.example</groupId>
    <artifactId>my-starter</artifactId>
    <version>1.0.0</version>
</dependency>
```

这个依赖会自动引入 Starter 类和其他必要的依赖，使你可以在项目中使用 `MyService` 类和其他相关的组件