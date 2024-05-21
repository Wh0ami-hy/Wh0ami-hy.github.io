---
layout: post   	
catalog: true 	
tags:
    - MyBatis
---


# 1. 简介

属于物理分页。pagehelper插件的基本原理是使用 MyBatis 提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的 sql，然后重写 sql，根据 dialect 方言，添加对应的物理分页语句和物理分页参数

MyBatis中的插件是通过拦截器来实现的，真正执行Sql的是四大对象：Executor，StatementHandler，ParameterHandler，ResultSetHandler。

当程序执行mapper方法时，就会被拦截器`PageInterceptor`拦截到

# 2. 在SpringBoot 中配置

## 2.1. 添加依赖

PageHelper自带了mybatis、mybatis-spring，不排除会报错循环依赖

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

## 2.2. 参数配置

在application.yml中配置分页插件的可选参数

```yml
pagehelper:  
  helper-dialect: mysql  
  reasonable: true  
  support-methods-arguments: true
  params: count=countSql
```

**下面几个参数都是针对默认 dialect 情况下的参数。使用自定义 dialect 实现时，下面的参数没有任何作用。**

- `helper-dialect`：配置`helper-dialect`属性来指定分页插件使用哪种方言（不配置的话pageHelper也会自动检测）。配置时，可以使用下面的缩写值：`oracle`,`mysql`,`mariadb`,`sqlite`,`hsqldb`,`postgresql`,`db2`,`sqlserver`,`informix`,`h2`,`sqlserver2012`,`derby`  **使用 SqlServer2012 数据库时，需要手动指定为 `sqlserver2012`，否则会使用 SqlServer2005 的方式进行分页  
- `offset-as-page-num`：默认值为 `false`，该参数对使用 `RowBounds` 作为分页参数时有效。 当该参数设置为 `true` 时，会将 `RowBounds` 中的 `offset` 参数当成 `pageNum` 使用，可以用页码和页面大小两个参数进行分页
- `row-bounds-with-count`：默认值为`false`，该参数对使用 `RowBounds` 作为分页参数时有效。 当该参数设置为`true`时，使用 `RowBounds` 分页会进行 count 查询
- `page-size-zero`：默认值为 `false`，当该参数设置为 `true` 时，如果 `pageSize=0` 或者 `RowBounds.limit = 0` 就会查询出全部的结果（相当于没有执行分页查询，但是返回结果仍然是 `Page` 类型）
- `reasonable`：分页合理化参数，默认值为`false`。当该参数设置为 `true` 时，`pageNum<=0` 时会查询第一页， `pageNum>pages`（超过总数时），会查询最后一页。默认`false` 时，直接根据参数进行查询，如果`pageNum<=0`或`pageNum>总页数`会返回空数据
- `params`：为了支持`startPage(Object params)`方法，增加了该参数来配置参数映射，用于从对象中根据属性名取值， 可以配置 `pageNum,pageSize,count,pageSizeZero,reasonable`，不配置映射的用默认值， 默认值为`pageNum=pageNum;pageSize=pageSize;count=countSql;reasonable=reasonable;pageSizeZero=pageSizeZero`
- `support-methods-arguments`：支持通过 Mapper 接口参数来传递分页参数，默认值`false`。当该参数设置为 `true` 时，分页插件会从查询方法的参数值中，自动根据上面 `params` 配置的字段中取值，查找到合适的值时就会自动分页
- `auto-runtime-dialect`：默认值为 `false`。设置为 `true` 时，允许在运行时根据多数据源自动识别对应方言的分页 （不支持自动选择`sqlserver2012`，只能使用`sqlserver`）
- `close-conn`：默认值为 `true`。当使用运行时动态数据源或没有设置 `helperDialect` 属性自动获取数据库类型时，会自动获取一个数据库连接， 通过该属性来设置是否关闭获取的这个连接，默认`true`关闭，设置为 `false` 后，不会关闭获取的连接，这个参数的设置要根据自己选择的数据源来决定

**重要提示：**

当 `offsetAsPageNum=false` 的时候，由于 `PageNum` 问题，`RowBounds`查询的时候 `reasonable` 会强制为 `false`。使用 `PageHelper.startPage` 方法不受影响。

## 2.3. 实现分页

-   使用 `PageHelper.startPage(pageNum,pageSize)` 开启分页
-   中间有个获取数据的方法，如`userInfoMapper.list()`
-   创建 `PageInfo pageInfo = new PageInfo<>(taskResultList)` 分页对象

关键点在于中间获取数据的方法，该方法很容易导致分页返回的总记录数 total 错误。如果中间获取数据的方法，只是一个简单的从数据库查询就返回，没有经过其他任何操作的话，分页是没有问题的。如果中间获取数据的方法，从数据库查询数据后，还要对查询到的数据进行处理，那么分页结果会有问题的

```java
public Result page (@PathVariab1e(value =”pageSize" ) Integer pageSize,@PathVariab1e(value =”pageNum" ) Integer pageNum) {
	//开启分页
	PageHelper.startPage(pageNum, pageSize);
	//获取数据
	List<TaskResult> taskResultList = taskResultService.list(); 
	//创建分页对象
	PageInfo pageInfo = new PageInfo<> (taskResultList); 
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

# 3. ThreadLocal问题（重点）

```java
 // 开始分页
 // @param pageNum      页码
 // @param pageSize     每页显示数量
 // @param count        是否进行count查询
 // @param reasonable   分页合理化,null时用默认配置
 // @param pageSizeZero true且pageSize=0时返回全部结果，false时分页,null时用默认配置
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

# 4. 重要提示

## 4.1. `PageHelper.startPage`方法重要提示

只有紧跟在`PageHelper.startPage`方法后的第一个Mybatis的查询（Select）方法会被分页。

## 4.2. 请不要配置多个分页插件

请不要在系统中配置多个分页插件(使用Spring时,`mybatis-config.xml`和`Spring<bean>`配置方式，请选择其中一种，不要同时配置多个分页插件)！

## 4.3. 分页插件不支持带有`for update`语句的分页

对于带有`for update`的sql，会抛出运行时异常，对于这样的sql建议手动分页，毕竟这样的sql需要重视。

## 4.4. 分页插件不支持嵌套结果映射

由于嵌套结果方式会导致结果集被折叠，因此分页查询的结果在折叠后总数会减少，所以无法保证分页结果数量正确。

# 5. pageHelper 实现一对多分页查询（重点）

## 5.1. 背景

解决pageHelper 不支持嵌套结果映射（一对多）的问题

假设我们有两个实体类：Order 和 OrderItem，它们之间的关系是一对多，即一个 Order 对应多个 OrderItem

现在需要联表查询，实现以主表数据的分页结果为基础进行联表查询

## 5.2. MySqlDialect类的分页原理

MysqlDialect里面有两个方法 getPageSql 和 processPageParameter

getPageSql 方法是为了在sql最后加上LIMIT语句

processPageParameter方法是为了添加分页参数到参数Map里

```sql
select eq.question_id, eq.content as content1, eq.type, eq.question_tag_id,eq.difficulty,eqi.question_item_id, eqi.content as content2,eqi.answer,eqi.question_id
from ex_question as eq
join ex_question_item as eqi on eq.question_id = eqi.question_id
```

对于以上sql，使用` Page page = PageHelper.startPage(pageNum, pageSize) `分页查询的时候分页插件就会在sql的最后面帮我们加上`limit ?,?`，然后进行分页查询。那就会产生分页查询不准确的问题。因为先对join查询数据做了分页（不是对主数据分页），查出数据后再映射一对多实体

正确的逻辑应该是如下：首先使用子查询（`eq_pre`）从`ex_question`表中选择了LIMIT之后的结果。然后，将子查询的结果与`ex_question_item`表进行联接，以获取所需的数据

```sql
select eq.question_id, eq.content as content1, eq.type, eq.question_tag_id, eq.difficulty, eqi.question_item_id, eqi.content as content2, eqi.answer, eqi.question_id  
from 
(select eq_pre.question_id, eq_pre.content, eq_pre.type, eq_pre.question_tag_id, eq_pre.difficulty  
from ex_question as eq_pre  
limit ?,?) as eq  
join ex_question_item as eqi on eq.question_id = eqi.question_id
```

## 5.3. 重写MySqlDialect

重写后生效需配置`pagehelper.helperDialect=实现类的全限定名称`

核心思路就是根据正则表达式匹配，然后把`limit?,?`语句和参数插入到合适的位置，实现对主表分页的语句

```java
@Slf4j  
public class PageMySqlDialectPlus extends MySqlDialect {  
//   只需注意/*fixed*/ 和/*limit*/ 这两个注释 fixed代表开启 limit代表插入位置这样就可以既可自定义 也可用原来的pageheper  
    @Override  
    public String getPageSql(String sql, Page page, CacheKey pageKey) {  
        Pattern compileFixed = Pattern.compile("/\\*fixed\\*/");  
        String compileLimit = "/\\*limit\\*/";  
  
        log.info("替换前的sql=" + sql);  
  
        if (compileFixed.matcher(sql).find()) {  
  
            if (page.getStartRow() == 0) {  
                sql = sql.replaceFirst(compileLimit, " LIMIT ? ");  
            } else {  
                sql = sql.replaceFirst(compileLimit, " LIMIT ?, ? ");  
  
            }  
            log.info("替换后的sql=" + sql);  
            return sql;  
        }  
        return super.getPageSql(sql, page, pageKey);  
    }  
}
```

只需注意`/*fixed*/` 和`/*limit*/` 这两个注释即可，fixed代表开启limit插入功能，limit代表插入位置，这样既可走自定义也可走原来的方式

```sql
/*fixed*/ select eq.question_id, eq.content as content1, eq.type, eq.question_tag_id, eq.difficulty, eqi.question_item_id, eqi.content as content2, eqi.answer, eqi.question_id  
from (select eq_pre.question_id, eq_pre.content, eq_pre.type, eq_pre.question_tag_id, eq_pre.difficulty  
from ex_question as eq_pre  
/*limit*/) as eq  
join ex_question_item as eqi on eq.question_id = eqi.question_id
```

