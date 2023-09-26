---
layout: post   	
catalog: true 	
tags:
    - Spring
---



# 1. 前言

一个后端接口大致分为四个部分组成：**接口地址（url）、接口请求方式（get、post等）、请求数据（request）、响应数据（response）**。

# 2. 参数校验

一个接口一般对参数（请求数据）都会进行安全校验

一般来说有三种常见的校验方式，我们使用了最简洁的第三种方法

- 业务层校验
- Validator + BindResult校验
- Validator + 自动抛出异常（推荐）

业务层校验无需多说，即手动在java的Service层进行数据校验判断。不过这样太繁琐了，光校验代码就会有很多

## 2.1. Validator 内置参数校验

| 注解             | 校验功能                           |
| :--------------- | :--------------------------------- |
| @AssertFalse     | 必须是false                        |
| @AssertTrue      | 必须是true                         |
| @DecimalMax      | 小于等于给定的值                   |
| @DecimalMin      | 大于等于给定的值                   |
| @Digits          | 可设定最大整数位数和最大小数位数   |
| @Email           | 校验是否符合Email格式              |
| @Future          | 必须是将来的时间                   |
| @FutureOrPresent | 当前或将来时间                     |
| @Max             | 最大值                             |
| @Min             | 最小值                             |
| @Negative        | 负数（不包括0）                    |
| @NegativeOrZero  | 负数或0                            |
| @NotBlank        | 不为null并且包含至少一个非空白字符 |
| @NotEmpty        | 不为null并且不为空                 |
| @NotNull         | 不为null                           |
| @Null            | 为null                             |
| @Past            | 必须是过去的时间                   |
| @PastOrPresent   | 必须是过去的时间，包含现在         |
| @PositiveOrZero  | 正数或0                            |
| @Size            | 校验容器的元素个数                 |

## 2.2. 分组校验和递归校验

分组校验有三个步骤：

- 定义一个分组类（或接口）
- 在校验注解上添加groups属性指定分组
- Controller方法的`@Validated`注解添加分组类

```java
public interface Update extends Default{  
}  
@Data  
public class User {  
    @NotNull(message = "用户id不能为空",groups = Update.class)  
    private Long id;  
  ......  
}  
@PostMapping("update")  
public String update(@Validated({Update.class}) User user) {  
    return "success";  
}
```

对于递归校验（比如类中类），只要在相应属性类上增加`@Valid`注解即可实现（对于集合同样适用）

## 2.3. 自定义校验

Spring Validation允许用户自定义校验，实现很简单，分两步：

- 自定义校验注解
- 编写校验者类

```java
@Target({ METHOD, FIELD, ANNOTATION_TYPE, CONSTRUCTOR, PARAMETER, TYPE_USE })  
@Retention(RUNTIME)  
@Documented  
@Constraint(validatedBy = {HaveNoBlankValidator.class})// 标明由哪个类执行校验逻辑  
public @interface HaveNoBlank {  
  
    // 校验出错时默认返回的消息  
    String message() default "字符串中不能含有空格";  
    Class<?>[] groups() default { };  
    Class<? extends Payload>[] payload() default { };  
    /**  
     * 同一个元素上指定多个该注解时使用  
     */  
    @Target({ METHOD, FIELD, ANNOTATION_TYPE, CONSTRUCTOR, PARAMETER, TYPE_USE })  
    @Retention(RUNTIME)  
    @Documented  
    public @interface List {  
        NotBlank[] value();  
    }  
}  
public class HaveNoBlankValidator implements ConstraintValidator<HaveNoBlank, String> {  
    @Override  
    public boolean isValid(String value, ConstraintValidatorContext context) {  
        // null 不做检验  
        if (value == null) {  
            return true;  
        }  
        // 校验失败  
        return !value.contains(" ");  
        // 校验成功  
    }  
}
```

# 3. 全局异常处理

参数校验失败会自动引发异常，我们当然不可能再去手动捕捉异常进行处理。但又要对这个异常进行处理，那正好使用SpringBoot全局异常处理来达到一劳永逸的效果
# 4. 数据统一响应

统一数据响应是我们自己自定义一个响应体类，无论后台是运行正常还是发生异常，响应给前端的数据格式是不变的，一般都是按照`code，msg，data`的格式返回的

首先可以设置一个枚举规范响应体中的响应码和响应信息

```java
@Getter
public enum ResultCode {
    SUCCESS(1000, "操作成功"),
    FAILED(1001, "响应失败"),
    VALIDATE_FAILED(1002, "参数校验失败"),
    ERROR(5000, "未知错误");
    private int code;
    private String msg;
    ResultCode(int code, String msg) {
        this.code = code;
        this.msg = msg;
    }
}
```
自定义响应体

```java
import lombok.Getter;
@Getter
public class ResultVO<T> {
    /**
     * 状态码，比如1000代表响应成功
     */
    private int code;
    /**
     * 响应信息，用来说明响应情况
     */
    private String msg;
    /**
     * 响应的具体数据
     */
    private T data;        public ResultVO(T data) {
        this(ResultCode.SUCCESS, data);
    }    public ResultVO(ResultCode resultCode, T data) {
        this.code = resultCode.getCode();
        this.msg = resultCode.getMsg();
        this.data = data;
    }
}

```

效果
```java
{  
    "code": 0,  
    "message": "success",  
    "data": {  
        "name": "Tom",  
        "age": 20,  
        "gender": "男"  
    }  
}
```

# 5. 接口版本控制

在spring boot项目中，如果要进行restful接口的版本控制一般有以下几个方向：

- 基于path的版本控制
- 基于header的版本控制

# 6. API接口安全

一般的解决方案有以下几点：

- Token授权认证，防止未授权用户获取数据；
    
- 时间戳超时机制；
    
- URL签名，防止请求参数被篡改；
    
- 防重放，防止接口被第二次请求，防采集；
    
- 采用HTTPS通信协议，防止数据明文传输；