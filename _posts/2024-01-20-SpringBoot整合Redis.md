---
layout: post   	
catalog: true 	
tags:
    - SpringBoot
---

# 1. 引入依赖
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```
# 2. Redis 配置

## 2.1. yml

```yml
spring:
  redis:
    host: xxx.xxx.99.232 # Redis服务器地址
    database: 0 # Redis数据库索引（默认为0）
    port: 6379 # Redis服务器连接端口
    password: xxx # Redis服务器连接密码（默认为空）
```

## 2.2. Java configuration

首先，使用jedis客户端，定义一个connectionFactory。然后使用jedisConnectionFactory定义一个RedisTemplate。这可以用于自定义存储库查询数据

```java
@Bean
JedisConnectionFactory jedisConnectionFactory() {
    JedisConnectionFactory jedisConFactory
      = new JedisConnectionFactory();
    jedisConFactory.setHostName("localhost");
    jedisConFactory.setPort(6379);
    return jedisConFactory;
}

@Bean
public RedisTemplate<String, Object> redisTemplate() {
    RedisTemplate<String, Object> template = new RedisTemplate<>();
    template.setConnectionFactory(jedisConnectionFactory());
    return template;
}
```

# 3. RedisTemplate

Spring Data Redis 提供的模板类，可以对 Redis 进行操作

RedisTemplate除了提供 opsForValue 方法来操作字符串之外，还提供了以下方法：

- opsForList：操作 list
- opsForSet：操作 set
- opsForZSet：操作有序 set
- opsForHash：操作 hash

```java
@SpringBootTest
class CodingmoreRedisApplicationTests {
    @Resource
    private RedisTemplate redisTemplate;

    @Resource
    private StringRedisTemplate stringRedisTemplate;

    @Test
    public void testRedis() {
        // 添加
        redisTemplate.opsForValue().set("name","沉默王二");
        // 查询
        System.out.println(redisTemplate.opsForValue().get("name"));
        // 删除
        redisTemplate.delete("name");
        // 更新
        redisTemplate.opsForValue().set("name","沉默王二的狗腿子");
        // 查询
        System.out.println(redisTemplate.opsForValue().get("name"));

        // 添加
        stringRedisTemplate.opsForValue().set("name","沉默王二");
        // 查询
        System.out.println(stringRedisTemplate.opsForValue().get("name"));
        // 删除
        stringRedisTemplate.delete("name");
        // 更新
        stringRedisTemplate.opsForValue().set("name","沉默王二的狗腿子");
        // 查询
        System.out.println(stringRedisTemplate.opsForValue().get("name"));

    }
}
```

# 4. Redis 连接池

- Jedis：Spring Boot 1.5.x 版本时默认的 Redis 客户端，实现上是直接连接 Redis Server，如果在多线程环境下是非线程安全的，这时候要使用连接池为每个 jedis 实例增加物理连接
- Lettuce：Spring Boot 2.x 版本后默认的 Redis 客户端，基于 Netty 实现，连接实例可以在多个线程间并发访问，一个连接实例不够的情况下也可以按需要增加连接实例

## 4.1. Lettuce

Lettuce是Spring Boot 2.x 版本后默认的，所以不许单独引入依赖

修改 application.yml，添加 Lettuce 连接池配置（pool 节点）。

```yml
spring:
    redis:
        lettuce:
          pool:
            max-active: 8 # 连接池最大连接数
            max-idle: 8 # 连接池最大空闲连接数
            min-idle: 0 # 连接池最小空闲连接数
            max-wait: -1ms # 连接池最大阻塞等待时间，负值表示没有限制
```
在 pom.xml 文件中添加 commons-pool2 依赖，否则会在启动的时候报 ClassNotFoundException 的错。这是因为 Spring Boot 2.x 里默认没启用连接池
```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-pool2</artifactId>
    <version>2.6.2</version>
    <type>jar</type>
    <scope>compile</scope>
</dependency>
```
测试代码
```java
GenericObjectPoolConfig poolConfig = new GenericObjectPoolConfig();
poolConfig.setMaxTotal(100); // 设置最大连接数

RedisURI redisURI = RedisURI.Builder.redis("localhost", 6379).build();
RedisClient redisClient = RedisClient.create(redisURI);
StatefulRedisConnection<String, String> connection = redisClient.connect();

try {
    RedisCommands<String, String> commands = connection.sync();
    // 进行Redis操作
} finally {
    if (connection != null) {
        connection.close(); // 归还连接给连接池
    }
    if (redisClient != null) {
        redisClient.shutdown(); // 关闭Redis客户端
    }
}
```
## 4.2. Jedis

在 pom.xml 文件中添加 Jedis 依赖，去除 Lettuce 默认依赖。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
    <exclusions>
        <exclusion>
            <groupId>io.lettuce</groupId>
            <artifactId>lettuce-core</artifactId>
        </exclusion>
    </exclusions>
</dependency>

<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
</dependency>
```

修改 application-dev.yml，添加 Jedis 连接池配置。

```
spring:
    redis:
        jedis:
          pool:
            max-active: 8 # 连接池最大连接数
            max-idle: 8 # 连接池最大空闲连接数
            min-idle: 0 # 连接池最小空闲连接数
            max-wait: -1ms # 连接池最大阻塞等待时间，负值表示没有限制
```

# 5. Spring Cache 操作 Redis

Spring Cache 是 Spring 提供的一整套的缓存解决方案。虽然它本身并没有提供缓存的实现，但是它提供了一整套的接口和代码规范、配置、注解等，这样它就可以整合各种缓存方案了，比如 Redis、Ehcache，我们也就不用关心操作缓存的细节。