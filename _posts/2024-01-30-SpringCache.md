---
layout: post   	
catalog: true 	
tags:
    - Spring
---


# 1. 简介

从 3.1 版本开始，Spring 框架提供了对现有 Spring 应用透明地添加缓存的支持。与事务支持类似，缓存抽象允许一致使用各种缓存解决方案，对代码的影响最小。

在 Spring Framework 4.1 中，缓存抽象得到了极大的扩展，支持 `JSR-107` 注解 和更多的自定义选项

Spring 提供的核心缓存抽象位于 `spring-context`模块中

# 2. 使用

如果是 Spring Boot 项目，可以利用 `spring-boot-starter-cache` 来轻松添加缓存依赖项：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
    <version>2.4.0</version>
</dependency>
```

该 Starter 包含了`spring-context-support`模块，它位于 `spring-context`模块之上，并提供了更多由 `EhCache`或 `Caffeine`支持的 `CacheManager`。

