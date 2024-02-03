---
layout: post   	
catalog: true 	
tags:
    - Spring
---


# 1. 简介

从 3.1 版本开始，Spring 框架提供了对现有 Spring 应用透明地添加缓存的支持。与`事务` 支持类似，缓存抽象允许一致使用各种缓存解决方案，对代码的影响最小。

在 Spring Framework 4.1 中，缓存抽象得到了极大的扩展，支持 `JSR-107` 注解 和更多的自定义选项

# 2. 使用

如果是 Spring Boot 项目，可以利用 `spring-boot-starter-cache` 来轻松添加缓存依赖项：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
    <version>2.4.0</version>
</dependency>
```