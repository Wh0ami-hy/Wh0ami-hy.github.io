---
layout: post   	
catalog: true 	
tags:
    - Java
---





# ORM

ORM (Object Relational Mapping,对象关系映射)是为了解决面向对象与
关系数据库存在的互不匹配现象的一种技术。
ORM通过使用描述对象和数据库之间映射的元数据将程序中的对象自动持久化到关系数据库中。
ORM框架的本质是简化编程中操作数据库的编码。

![QQ截图20230107201351](F:\笔记\博客\文章图片\QQ截图20230107201351.png)

# MyBatisPlus

MyBatis是一款优秀的数据持久层ORM框架，被广泛地应用于应用系统。

MyBatis能够非常灵活地实现动态SQL，可以使用XML或注解来配置和映射原生信息，能够轻松地将Java的POJO (Plain Ordinary Java Object，普通的Java对象)与数据库中的表和字段进行映射关联。

MyBatis-Plus是一个MyBatis的增强工具，在MyBatis的基础上做了增强，简化了开发。

## 配置

### 添加依赖

```xml
		<!-- MyBatisP1us依赖 -->
		<dependency>
			<groupId>com.baomidou</groupId>
			<artifactId>mybatis-plus-boot-starter</artifactId>
			<version>3.4.2</version>
		</dependency>
		<!-- mysq1驱动依赖 -->
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<version>5.1.48</version>
		</dependency>
		<!--
        数据连接池druid
        -->
		<dependency>
			<groupId>com.alibaba</groupId>
			<artifactId>druid-spring-boot-starter </artifactId>
			<version>1.1.20</version>
		</dependency>
```

### 全局配置

配置数据库相关信息。

```java
#mysql配置
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/demo?useSSL=false
spring.datasource.username=root
spring.datasource.password=root
mybatis-plus.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl
```

### 添加@MapperScan注解

创建package：mapper（存放操作数据库的接口interface）

在Spring Boot启动类中添加@MapperScan 注解，扫描mapper文件夹

```java
@SpringBootApplication
@MapperScan("com.example.vue_music.mapper")
public class MybatisplusDemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(MybatisplusDemoApplication.class, args);
    }
}
```

## Mybatis CRUD注解

| 注解     | 功能                 |
| -------- | -------------------- |
| @Insert  |                      |
| @Update  |                      |
| @Delete  |                      |
| @Select  |                      |
| @Result  | 实现结果集封装       |
| @Results | 封装多个结果集       |
| @One     | 实现一对一结果集封装 |
| @Many    | 实现一对多结果集封装 |

## Mybatis CRUD操作

```java
@Mapper
pub1ic interface UserMapper {
@Insert("insert into user values (#{id}，#{username} ,#{password}，#{birthday})")
int add(User user);
@Update("update user set username=# {username} ,password=#{password} ,birthday=#{birthgay} where id=#{id}")
int update(User user);
@Delete("delete from user where id=#{id}")
int delete(int id);
@Select("select * from user where id=#{id}")
User findById(int id) ;
@Se1ect("select * from user") 
List<User> getA11();
}
```

## MyBatisPlus 单表查询

更多操作请看官网

```
https://baomidou.com/pages/
```

在mapper下的接口中

```java
@Mapper
public interface UserMapper extends BaseMapper <User>{ //mybatisPlus会自动根据所提供的类去找表，前提类名和表名要一致
}

```

在entity中

当实体类名与表名不一致时，可用注解 @TableName("表名")  ，告诉MybatisPlus 要操作的表名

注解 @TableField("表名") 

在controller中

使用@Autowired 实现自动注入实例化的接口，然后调用方法即可

```java
@CrossOrigin
@RestController
@RequestMapping("/student")
public class StudentController {
    @Autowired
    private StudentMapper studentMapper;

    @GetMapping("/show")
    public Result show() {

        List<Student> students = studentMapper.show();
        
        return Result.ok().data("data",students);
    }
}
```

## MyBatis 多表查询

MyBatisPlus仅对单表查询做了强化处理，对于多表查询，仍是MyBatis的方法

## MyBatisPlus 分页查询

创建package：config（存放配置类）

新建配置类 MyBatisPlusConfig

```java
@Configuration
    public class MyBatisPlusConfig {
    @Bean
    public MybatisPlusInterceptor paginationInterceptor() {
        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
        PaginationInnerInterceptor paginationInterceptor = new PaginationInnerInterceptor(DbType.MYSQL);//选择数据库类型
        interceptor.addInnerInterceptor(paginationInterceptor);
        return interceptor;
    }
}
```





