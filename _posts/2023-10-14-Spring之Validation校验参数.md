---
layout: post   	
catalog: true 	
tags:
    - Spring
---


# 1. 概念区分

`@Valid`是Java标准的Bean验证注解，而`@Validated`是Spring框架提供的增强验证注解，具有更多特性，如分组验证。
# 2. 环境说明

从springboot-2.3开始，校验包被独立成了一个starter组件，所以需要引入如下依赖：

```xml
<dependency>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-validation</artifactId>  
</dependency>
```
# 3. Validator 内置参数校验

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
| @NotEmpty        | 不为null并且不为空（不能校验数字类型）                 |
| @NotNull         | 不为null                           |
| @Null            | 为null                             |
| @Past            | 必须是过去的时间                   |
| @PastOrPresent   | 必须是过去的时间，包含现在         |
| @PositiveOrZero  | 正数或0                            |
| @Size            | 校验容器的元素个数                 |

对于String类型，建议使用`@NotBlank`

对于Integer类型，建议使用`@NotNull`，因为它的值要么为 `null`，要么为非 `null`，无需再进一步限制

对于Map、List类型 可以使用`@NotEmpty`，确保不为空

# 4. 常见的校验方式

**业务层校验**

手动在Java的Service层进行数据校验判断

```java
public String add(UserVO userVO) {
    if(userVO.getAge() == null){
        return "年龄不能为空";
    }
    if(userVO.getAge() > 120){
        return "年龄不能超过120";
    }
    // 省略一堆参数校验...
    return "OK";
}
```

**Validator + BindingResult**
....
....
**Validator + 自动抛出异常（推荐）**

使用全局异常处理代替BindingResult捕获异常

# 5. 在Controller层用validator做参数校验

**Bean字段校验**

Bean字段校验是对Java Bean对象中的字段进行验证。如，验证一个用户对象的姓名是否非空、年龄是否在有效范围内，或者电子邮件地址是否符合规定的格式。

首先在待校验字段上增加校验规则注解
```java
public class UserVO {
    @NotNull(message = "age 不能为空")
    private Integer age;
}
```

然后在`controller`方法中添加`@Validated`和用于接收错误信息的`BindingResult`就可以了
```java
public String add(@Validated UserVO userVO, BindingResult result) {
    List<FieldError> fieldErrors = result.getFieldErrors();
    if(!fieldErrors.isEmpty()){
        return fieldErrors.get(0).getDefaultMessage();
    }
    return "OK";
}
```

**单个参数校验**

先在`controller`类上添加注解`@Validated`

然后在方法中的待校验参数前增加校验规则注解和用于接收错误信息的`BindingResult`就可以了

```java
@Validated
@RestController
public class UserController{
	...
	...
	public String add(@NotNull(message = "不能为空") String uname, BindingResult result) {
	    List<FieldError> fieldErrors = result.getFieldErrors();
	    if(!fieldErrors.isEmpty()){
	        return fieldErrors.get(0).getDefaultMessage();
	    }
	    return "OK";
	}
}
```
# 6. 在Controller层之外用validator做参数校验

在controller层参数的注解用`@Valid`和`@Validated`都可生效，个人觉得最好还是统一用`@Validated`

在service层或其他spring管理的bean里使用。在controller之外用的话想让切面生效目前只有一种选择，直接在类上加`@Validated`注解（虽然这个注解可以加在方法上但是只在方法上加不会走切面逻辑，在方法上加主要是定义组校验逻辑）。加在类上会导致所有public方法在外部被调用时都走一次校验前置处理逻辑，一定程度上来说会造成一些不必要的性能影响，所以如果不是所有方法都需要校验并且对细微的性能影响有要求的话最好还是慎重考虑

# 7. 全局异常处理

如果每个`Controller`方法中都写一遍对`BindingResult`信息的处理，使用起来还是很繁琐。可以通过全局异常处理的方式统一处理校验异常。

当我们写了`@validated`注解，不写`BindingResult`的时候，Spring 就会抛出异常。由此，可以写一个全局异常处理类来统一处理这种校验异常，从而免去重复组织异常信息的代码。

总结了三种参数校验时可能引发的异常：
- 使用form data方式调用接口，校验异常抛出 BindException
- 使用 json 请求体调用接口，校验异常抛出 MethodArgumentNotValidException
- 单个参数校验异常抛出 ConstraintViolationException

全局异常处理类可以添加各种需要处理的异常，比如添加一个对`Exception.class`的异常处理，当所有`ExceptionHandler`都无法处理时，由其记录异常信息，并返回友好提示。

```java
@RestControllerAdvice
public class GlobalControllerAdvice {
    private static final String BAD_REQUEST_MSG = "客户端请求参数错误";
    // <1> 处理 form data方式调用接口校验失败抛出的异常 
    @ExceptionHandler(BindException.class)
    // ResultInfo是封装的一个结果类
    public ResultInfo bindExceptionHandler(BindException e) {
        List<FieldError> fieldErrors = e.getBindingResult().getFieldErrors();
        List<String> collect = fieldErrors.stream()
                .map(o -> o.getDefaultMessage())
                .collect(Collectors.toList());
        return new ResultInfo().success(HttpStatus.BAD_REQUEST.value(), BAD_REQUEST_MSG, collect);
    }
    // <2> 处理 json 请求体调用接口校验失败抛出的异常 
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResultInfo methodArgumentNotValidExceptionHandler(MethodArgumentNotValidException e) {
        List<FieldError> fieldErrors = e.getBindingResult().getFieldErrors();
        List<String> collect = fieldErrors.stream()
                .map(o -> o.getDefaultMessage())
                .collect(Collectors.toList());
        return new ResultInfo().success(HttpStatus.BAD_REQUEST.value(), BAD_REQUEST_MSG, collect);
    }
    // <3> 处理单个参数校验失败抛出的异常
    @ExceptionHandler(ConstraintViolationException.class)
    public ResultInfo constraintViolationExceptionHandler(ConstraintViolationException e) {
        Set<ConstraintViolation<?>> constraintViolations = e.getConstraintViolations();
        List<String> collect = constraintViolations.stream()
                .map(o -> o.getMessage())
                .collect(Collectors.toList());
        return new ResultInfo().success(HttpStatus.BAD_REQUEST.value(), BAD_REQUEST_MSG, collect);
    }
}
```

# 8. 其他校验类型

## 8.1. 分组校验

同一个参数在新增的时候为必填，在更新的时候又非必填。面对这种场景就需要用到分组校验了

**先定义一个分组接口**

```java
public interface ValidGroup extends Default {
  
    interface Crud extends ValidGroup{
        interface Create extends Crud{

        }

        interface Update extends Crud{

        }

        interface Query extends Crud{

        }

        interface Delete extends Crud{

        }
    }
}
```

我们定义一个分组接口ValidGroup让其继承`javax.validation.groups.Default`，再在分组接口中定义出多个不同的操作类型，Create，Update，Query，Delete

**在校验注解上添加`groups`属性指定分组**

```java
public class UserVO {
    @NotBlank(message = "name 不能为空",groups = ValidGroup.Crud.Update.class)
    @NotNull(message = "name 不能为空",groups = ValidGroup.Crud.Create.class)
    private String name;
    // 省略其他代码...
}
```

给参数指定分组，对于未指定分组的则使用的是默认分组

**`Controller`方法的`@Validated`注解添加分组类**

```java
@PostMapping("update")
public ResultInfo update(@Validated(value = ValidGroup.Crud.Update.class) UserVO userVO) {
    return new ResultInfo().success(userVO);
}
```

注：`@validated`和Validator 内置的参数校验注解默认都属于`Default.class`分组

## 8.2. 递归校验

如果 UserVO 类中增加一个 OrderVO 类的属性，而 OrderVO 中的属性也需要校验，就用到递归校验了，只要在相应属性上增加`@Valid`注解即可实现（对于集合同样适用）
```java
public class UserVO {
    @NotBlank(message = "name 不能为空",groups = Update.class)
    private String name;
    //需要递归校验的OrderVO
    @Valid
    private OrderVO orderVO;
    // 省略其他代码...
}   

```

## 8.3. 自定义校验

先自定义校验注解

```java
@Target({METHOD, FIELD, ANNOTATION_TYPE, CONSTRUCTOR, PARAMETER})
@Retention(RUNTIME)
@Documented
@Constraint(validatedBy = {HaveNoBlankValidator.class})// 标明由哪个类执行校验逻辑
public @interface HaveNoBlank {
    
    // 校验出错时默认返回的消息
    String message() default "字符串中不能含有空格";

    Class<?>[] groups() default { };

    Class<? extends Payload>[] payload() default { };

    /**
     * 同一个元素上指定多个该注解时使用
     */
    @Target({ METHOD, FIELD, ANNOTATION_TYPE, CONSTRUCTOR, PARAMETER, TYPE_USE })
    @Retention(RUNTIME)
    @Documented
    public @interface List {
        NotBlank[] value();
    }
}

```

再编写校验者类

```java
public class HaveNoBlankValidator implements ConstraintValidator<HaveNoBlank, String> {
    @Override
    public boolean isValid(String value, ConstraintValidatorContext context) {
        // null 不做检验
        if (value == null) {
            return true;
        }
        if (value.contains(" ")) {
            // 校验失败
            return false;
        }
        // 校验成功
        return true;
    }
}

```

自定义校验注解使用起来和内置注解无异，在需要的字段上添加相应注解即可

# 9. 继承问题

父类中的参数校验，能否被子类继承？