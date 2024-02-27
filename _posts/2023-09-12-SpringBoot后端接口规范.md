---
layout: post   	
catalog: true 	
tags:
    - SpringBoot
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

自定义响应体

```java
public class Result<T> {
    private int code;
    private String message;
    private T data;

    public Result(int code, String message, T data) {
        this.code = code;
        this.message = message;
        this.data = data;
    }

    public int getCode() {
        return code;
    }

    public String getMessage() {
        return message;
    }

    public T getData() {
        return data;
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

接口是需要考虑幂等性的，尤其抢红包、转账这些重要接口。最直观的业务场景，就是**用户连着点击两次**，你的接口有没有**hold住**。或者消息队列出现重复消费的情况，你的业务逻辑怎么控制？

**防重和幂等设计其实是有区别的**。防重主要为了避免产生重复数据，把重复请求拦截下来即可。而幂等设计除了拦截重复的请求，还要求每次相同的请求都返回一样的效果。不过呢，很多时候，它们的处理流程、方案是类似的

接口幂等实现方案主要有8种：

-   select+insert+主键/唯一索引冲突
    
-   直接insert + 主键/唯一索引冲突
    
-   状态机幂等
    
-   抽取防重表
    
-   token令牌
    
-   悲观锁
    
-   乐观锁
    
-   分布式锁

# 8. 接口防重处理

前端重复请求。如果是查询类的请求，其实不用防重。如果是更新修改类的话，尤其金融转账类的，就要过滤重复请求了。简单点，你可以使用Redis防重复请求，同样的请求方，一定时间间隔内的相同请求，考虑是否过滤。并发不高的话，**推荐使用数据库防重表**，以**唯一流水号作为主键或者唯一索引**。

接口幂等性和防重处理是相互关联的，需要同时考虑和实现，以确保系统的稳定性、数据的一致性和操作的正确性
