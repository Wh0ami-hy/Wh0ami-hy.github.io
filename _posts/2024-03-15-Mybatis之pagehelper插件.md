---
layout: post   	
catalog: true 	
tags:
    - MyBatis
---


# 1. 简介

属于物理分页。分页插件的基本原理是使用 MyBatis 提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的 sql，然后重写 sql，根据 dialect 方言，添加对应的物理分页语句和物理分页参数

# 2. 使用

**非 SpringBoot 项目中使用**

添加依赖

```xml
<dependency>
	<groupId>com.github.pagehelper</groupId>
	<artifactId>pagehelper</artifactId>
	<version>5.2.0</version>
</dependency>
```

在MyBatis的核心配置文件（mybatis-config.xml）中配置插件或者以JavaConfig 方式

```xml
<plugins>
	<!--设置分页插件-->
	<plugin interceptor="com.github.pagehelper.PageInterceptor"></plugin>
</plugins>
```

**在SpringBoot 项目中使用**

添加依赖（PageHelper自带了mybatis、mybatis-spring，不排除会报错循环依赖）

```xml
<dependency> 
	<groupId>com.github.pagehelper</groupId> 
	<artifactId>pagehelper-spring-boot-starter</artifactId> 
	<version>1.2.10</version> 
	<exclusions>
        <exclusion>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
        </exclusion>
        <exclusion>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

在spring boot的application.yml中添加配置

```yml
pagehelper:  
  helper-dialect: mysql  
  reasonable: true  
  support-methods-arguments: true
```

`helper-dialect` 配置使用哪种数据库语言，不配置的话pageHelper也会自动检测

`reasonable` 配置分页参数合理化功能，默认是false。 启用合理化时，如果pageNum<1会查询第一页，如果pageNum>总页数会查询最后一页。禁用合理化时，如果pageNum<1或pageNum>总页数会返回空数据。

`params` 为了支持startPage(Object params)方法，增加了该参数来配置参数映射，用于从对象中根据属性名取值。可以配置 pageNum、pageSize、count、pageSizeZero、reasonable，不配置映射的用默认值， 默认值为pageNum=pageNum、pageSize=pageSize、count=countSql、reasonable=reasonable、pageSizeZero=pageSizeZero

`support-methods-arguments` 支持通过Mapper接口参数来传递分页参数，默认值false，分页插件会从查询方法的参数值中，自动根据上面 params 配置的字段中取值，查找到合适的值时就会自动分页。

**实现分页功能**

-   使用 `PageHelper.startPage(pageNum,pageSize)` 开启分页
-   中间有个获取数据的方法，如`userInfoMapper.list()`
-   创建 `PageInfo pageInfo = new PageInfo<>(taskResultList)` 分页对象

关键点在于中间获取数据的方法，该方法很容易导致分页返回的总记录数 total 错误

如果中间获取数据的方法，只是一个简单的从数据库查询就返回，没有经过其他任何操作的话，分页是没有问题的

如果中间获取数据的方法，从数据库查询数据后，还要对查询的数据进行处理，那么分页结果会有问题的

```java
public Result page (@PathVariab1e(value =”pageSize" ) Integer pageSize,@PathVariab1e(value =”pageNum" ) Integer pageNum) {
	PageHelper.startPage(pageNum, pageSize) ;//开启 分页
	List<TaskResult> taskResultList = taskResultService.list(); //获取数据
	PageInfo pageInfo = new PageInfo<> (taskResultList); //创建 分页对象
	return new Result(true,200,"查询成功",pageInfo);
}
```

**注意事项**

在mapper.xml中编写sql语句的时候不要在末尾加分号，因为sql语句后面要拼接limit分页语句

PageHelper 里面的 PageHelper.startPage(1,10) 只对在 PageHelper 方法调用后紧跟的 MyBatis 查询方法得到的数据进行分页

```java
PageHelper.startPage(pageNum,pageSize);
List<BrandBo> brands= brandMapper.getBrand();
```

**分页类 PageInfo 的常用属性**

- pageNum：当前页的页码（从1开始）。
- pageSize：每页显示的条数。
- size：当前页显示的真实条数。
- total：总记录数。
- pages：总页数（由pageSize和total决定）。
- prePage：上一页的页码。
- nextPage：下一页的页码。
- isFirstPage：是否为第一页。
- isLastPage：是否为最后一页。
- hasPreviousPage：是否存在上一页。
- hasNextPage：是否存在下一页。
- navigatePages：导航分页的页码数，即在页面上同时显示的页码数量。

```
假设总共有10页数据，而navigatePages设置为5：

当前页为第1页时，导航分页的页码为[1, 2, 3, 4, 5]。
当前页为第3页时，导航分页的页码为[1, 2, 3, 4, 5]。
当前页为第5页时，导航分页的页码为[3, 4, 5, 6, 7]。
当前页为第8页时，导航分页的页码为[6, 7, 8, 9, 10]。
当前页为第10页时，导航分页的页码为[6, 7, 8, 9, 10]
```
- navigatepageNums：导航分页的页码列表，包含从起始页码到结束页码的所有页码

# 3. 原理

MyBatis中的插件是通过拦截器来实现的，真正执行Sql的是四大对象：Executor，StatementHandler，ParameterHandler，ResultSetHandler。

当程序执行sql接口mapper的方法时，就会被拦截器`PageInterceptor`拦截到

# 4. 注意事项

```java
/**
 * 开始分页
 *
 * @param pageNum      页码
 * @param pageSize     每页显示数量
 * @param count        是否进行count查询
 * @param reasonable   分页合理化,null时用默认配置
 * @param pageSizeZero true且pageSize=0时返回全部结果，false时分页,null时用默认配置
 */
public static <E> Page<E> startPage(int pageNum, int pageSize, boolean count, Boolean reasonable, Boolean pageSizeZero) {  
    Page<E> page = new Page(pageNum, pageSize, count);  
    page.setReasonable(reasonable);  
    page.setPageSizeZero(pageSizeZero);
    // 1、获取本地分页  
    Page<E> oldPage = getLocalPage();  
    if (oldPage != null && oldPage.isOrderByOnly()) {  
        page.setOrderBy(oldPage.getOrderBy());  
    }  
	// 2、设置本地分页
    setLocalPage(page);  
    return page;  
}
```

分别来看下：`getLocalPage()`和`setLocalPage(page)`，

**getLocalPage()**

```java
public static <T> Page<T> getLocalPage() {  
    return (Page)LOCAL_PAGE.get();  
}
```

常量 `LOCAL_PAGE`是ThreadLocal，独属于每个线程的本地缓存对象

```java
protected static final ThreadLocal<Page> LOCAL_PAGE = new ThreadLocal();
```

当一个请求来的时候，会获取持有当前请求的线程的ThreadLocal，调用`LOCAL_PAGE.get()`，查看当前线程是否有未执行的分页配置

**setLocalPage(page)**

设置线程的分页配置

```java
protected static void setLocalPage(Page page) {
    LOCAL_PAGE.set(page);
}
```

**Intercept方法**

pageHelper通过`PageInterceptor`实现分页效果，我们只需要关注Intercept方法

```java
@Override
public Object intercept(Invocation invocation) throws Throwable {
    try {
        Object[] args = invocation.getArgs();
        MappedStatement ms = (MappedStatement) args[0];
        Object parameter = args[1];
        RowBounds rowBounds = (RowBounds) args[2];
        ResultHandler resultHandler = (ResultHandler) args[3];
        Executor executor = (Executor) invocation.getTarget();
        CacheKey cacheKey;
        BoundSql boundSql;
        // 由于逻辑关系，只会进入一次
        if (args.length == 4) {
            //4 个参数时
            boundSql = ms.getBoundSql(parameter);
            cacheKey = executor.createCacheKey(ms, parameter, rowBounds, boundSql);
        } else {
            //6 个参数时
            cacheKey = (CacheKey) args[4];
            boundSql = (BoundSql) args[5];
        }
        checkDialectExists();
        //对 boundSql 的拦截处理
        if (dialect instanceof BoundSqlInterceptor.Chain) {
            boundSql = ((BoundSqlInterceptor.Chain) dialect).doBoundSql(BoundSqlInterceptor.Type.ORIGINAL, boundSql, cacheKey);
        }
        List resultList;
        //调用方法判断是否需要进行分页，如果不需要，直接返回结果
        if (!dialect.skip(ms, parameter, rowBounds)) {
            //判断是否需要进行 count 查询
            if (dialect.beforeCount(ms, parameter, rowBounds)) {
                //查询总数
                Long count = count(executor, ms, parameter, rowBounds, null, boundSql);
                //处理查询总数，返回 true 时继续分页查询，false 时直接返回
                if (!dialect.afterCount(count, parameter, rowBounds)) {
                    //当查询总数为 0 时，直接返回空的结果
                    return dialect.afterPage(new ArrayList(), parameter, rowBounds);
                }
            }
            resultList = ExecutorUtil.pageQuery(dialect, executor,
                    ms, parameter, rowBounds, resultHandler, boundSql, cacheKey);
        } else {
            //rowBounds用参数值，不使用分页插件处理时，仍然支持默认的内存分页
            resultList = executor.query(ms, parameter, rowBounds, resultHandler, cacheKey, boundSql);
        }
        return dialect.afterPage(resultList, parameter, rowBounds);
    } finally {
        if(dialect != null){
            dialect.afterAll();
        }
    }
}
```

在Intercept方法的最后，会在sql方法执行完成后，清理page缓存：

```java
finally {
    if(dialect != null){
        dialect.afterAll();
    }
}
```

看看这个`afterAll()`方法:

```java
@Override
public void afterAll() {
    //这个方法即使不分页也会被执行，所以要判断 null
    AbstractHelperDialect delegate = autoDialect.getDelegate();
    if (delegate != null) {
        delegate.afterAll();
        autoDialect.clearDelegate();
    }
    clearPage();
}
```

如果使用了`startPage()`，但是没有执行对应的sql，那么就表明，当前线程ThreadLocal被设置了分页参数，可是没有被使用，当下一个使用此线程的请求来时，就可能会出现问题（为什么是可能出现问题呢，继续往下看）。

如果程序在执行sql前，发生异常了，就没办法执行finally当中的`clearPage()`方法，也会造成线程的ThreadLocal被污染。

所以，官方给我们的建议，在使用PageHelper进行分页时，执行sql的代码要紧跟`startPage()`方法。

除此之外，我们可以手动调用`clearPage()`方法，在存在问题的方法之前。

> 需要注意：不要分页的方法前手动调用clearPage，将会导致你的分页出现问题。

**为什么是可能出现问题呢**

这个取决于我们启动服务所使用的容器，比如tomcat，在其内部处理请求是通过线程池的方式。

假设线程1持有没有被清除的page参数，不断调用同一个方法，后面两个请求使用的是线程2和线程3没有问题，再一个请求轮到线程1了，此时就会出现问题了。
