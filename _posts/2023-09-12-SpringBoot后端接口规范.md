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

一般来说有三种常见的校验方式

- 业务层校验
- Validator + BindingResult
- Validator + 自动抛出异常（推荐）

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
    private T data;        
    
    public ResultVO(T data) {
        this(ResultCode.SUCCESS, data);
    }    
    
    public ResultVO(ResultCode resultCode, T data) {
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

在spring MVC下，url映射到哪个method是由`RequestMappingHandlerMapping`来控制的，那么我们也是通过 `RequestMappingHandlerMapping`来做版本控制的。

# 6. API接口安全

一般的解决方案有以下几点：

- Token授权认证，防止未授权用户获取数据；
- 时间戳超时机制；
- URL签名，防止请求参数被篡改；
- 防重放，防止接口被第二次请求，防采集；
- 采用HTTPS通信协议，防止数据明文传输；

# 7. 接口幂等性

