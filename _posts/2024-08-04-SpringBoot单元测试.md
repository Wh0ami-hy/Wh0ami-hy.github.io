---
layout: post   	
catalog: true 	
tags:
    - SpringBoot
---

# 1. 单元测试

软件测试分为多种类型，但开发人员一般只涉及单元测试和集成测试

单元测试用于验证相关的一小段代码是否正常工作。传统的单元测试，即是测试一个函数是否正确运行。单元测试可以**为这个函数预先伪造一个测试环境**，例如用户登录了，且已经有超管权限了，那么运行这个函数是否能够得到我们期望得到的结果

单元测试就是一部分代码，但是它

- 不会在正常的业务流程中被执行
- 不被打包进入最终的编译程序
- 不会被任何其他业务代码以任何方式导入
- 不会影响正常的代码

当然，它通常还要满足下面这些条件

- 自动化的，不需要人工输入任何数据即可完成
- 独立的，任何两个单元测试之间都不应该发生调用关系
- 可重复的，单元测试可以无限重复执行且结果应该一致

# 2. spring-boot-starter-test

SpringBoot中有关测试的框架，主要来源于 spring-boot-starter-test。一旦依赖了spring-boot-starter-test，下面这些类库将被一同依赖进去：

- **JUnit**：java测试事实上的标准。
- **Spring Test & Spring Boot Test**：Spring的测试支持。
- **AssertJ**：提供了流式的断言方式。
- **Hamcrest**：提供了丰富的matcher。
- **Mockito**：mock框架，可以按类型创建mock对象，可以根据方法参数指定特定的响应，也支持对于mock调用过程的断言。
- **JSONassert**：为JSON提供了断言功能。
- **JsonPath**：为JSON提供了XPATH功能。

# 3. 插件 Squaretest

强烈推荐这个自动生成单元测试代码的插件

在选中类右键`Generate -> Generate Test` 后，不光能生成测试类和方法，甚至连Mockito 数据、方法和 Assertions 等都写好了，只需要自己改一改即可。


要测试哪个类，**点进当前类，`右键->Go To->Test->Create New Test`，在 Testing library 中选择 Junit5，则在对应目录生成测试类和方法。**

# 4. 实践

单元测试不需要启动项目，也不会连接数据库、RPC注 册中心等，但是相应的所有数据都需要打桩 Mock

Squaretest生成的单元测试方法都是只能生成public的

生成的代码说明，以查询方法SelectGiftLis为例：如果是测试service接口，一般使用serviceImpl来写测试用例，serviceImpl中一般会引入mapper接口。

测试代码中一般都是先生成一个对象，传入mapper接口方法的参数中，并且会创建一个mapper接口的返回对象。然后在使用serviceImpl来测试查

询方法，mock一个同样的查询参数，serviceImpl查询时就会有返回值。最后比对mapper接口返回的结果和serviceImpl查询返回的结果是否一致。

一致则通过测试，不一致则测试不通过。