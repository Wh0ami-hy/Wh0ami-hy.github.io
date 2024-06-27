---
layout: post   	
catalog: true 	
tags:
    - SpringBoot
---

# 1. 异步配置

在SpringBoot中简单使用异步编程非常简单，只需要两步

**使用`@EnableAsync`开启异步支持**

```java
@EnableAsync
@Configuration
public class ConcurrencyConfig {
...
}
```

**使用`@Async`注解相关方法**

```java
@Async
public void runAsync(Integer id){
...
}
```

注意，使用`@Async`标记的方法必须是`public`的，而且返回值必须是`void`或者`Future`

# 2. 线程池

## 2.1. ThreadPoolExecutor

`ThreadPoolExecutor`，它是java提供的类，一般会使用下面这个重载版本

```java
public ThreadPoolExecutor(int corePoolSize,
    int maximumPoolSize, long keepAliveTime,
    TimeUnit unit, BlockingQueue <
    Runnable > workQueue,
    RejectedExecutionHandler handler
)
{
    this(corePoolSize,
        maximumPoolSize,
        keepAliveTime, unit,
        workQueue, Executors.defaultThreadFactory(),
        handler);
}
```

**corePoolSize**

线程池中核心线程数目，会一直驻留在线程池中（除非设置allowCoreThreadTimeOut为true，默认为false）。

**maximumPoolSize**

整个线程池允许创建的最大线程数，这个数目包含核心线程数。例如其设置为5，corePoolSize设置为3，那么最多可以再创建2个线程。

**workQueue**

当新任务到来时，如果没有闲着的核心线程，任务首先会被存放在队列中。

**keepAliveTime**

那些闲着的非核心线程的存活时间

**unit**

keepAliveTime 参数的时间单位。

**handler**

饱和策略，当线程池没有能力再接收新任务时的处理策略，平台为我们预定义了4种

- AbortPolicy：直接抛`RejectedExecutionException`异常，告知程序线程池已经满负荷了，无法接收新任务

- CallerRunsPolicy：让调用线程池的那个线程执行新任务。其实就是因为线程池满负荷了没法执行，它自己把任务执行了。

- DiscardOldestPolicy：将任务队列队首第一个任务给丢弃掉，腾出个位置给新任务。

- DiscardPolicy：默默的把新任务扔了，连个水花都没有...

**前三个参数最为重要**

配置线程池最大线程个数的一个公式，但是这个只做参考

- CPU 密集型任务（CPU 核心数+1）
- I/O 密集型任务（2xCPU 核心数）
## 2.2. ThreadPoolTaskExecutor

Spring提供了一个`ThreadPoolExecutor`的包装类`ThreadPoolTaskExecutor`，我们在Spring程序中一般使用这个类，各个参数含义与ThreadPoolExecutor几乎一样

**在配置类中申明一个TaskExecutor类型的Bean**

```java
@EnableAsync
@Configuration
public class ConcurrencyConfig {
    @Bean
    public TaskExecutor threadPoolExecutorCpu(){
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(2);
        executor.setMaxPoolSize(3);
        executor.setQueueCapacity(2);
        executor.setKeepAliveSeconds(1);
        executor.setWaitForTasksToCompleteOnShutdown(true);
        executor.setThreadNamePrefix("task-thread-");
        executor.setRejectedExecutionHandler(new ThreadPoolExecutor.AbortPolicy());

        executor.initialize();
        return executor;
    }
}
```

**将线程池配置给`@Async`**

```java
@Async("threadPoolExecutorCpu")
public void runAsync(Integer id){
    log.info("start:{},num:{}",Thread.currentThread().getId(),id);
    try {
        Thread.sleep(3000);
    } catch (InterruptedException e) {
        throw new RuntimeException(e);
    }
    log.info("end:{},num:{}",Thread.currentThread().getId(),id);
}
```

## 2.3. 验证线程池配置

```java
@GetMapping("/run-async")
 public String runAsync(@RequestParam("count") Integer count) {
     List<Integer> collect = IntStream.rangeClosed(1, count).boxed().collect(Collectors.toList());

     for (int i : collect) {
         new Thread(() -> concurrencyService.runAsync(i)).start();
         try {
             Thread.sleep(200);
         } catch (InterruptedException e) {
             log.error("error", e);
         }
     }
     return "ok";
 }
```

# 3. `@Async` 使用

Spring 使用动态代理来使`@Async`其作用，所以要求其修饰的方法必须为`public`级别，且不能在同一个类调用。其修饰的方法返回值必须是`void`或者`Future`。所以在必要的时候，我们可以返回`CompletableFuture`，然后使用其强大的功能完成异步工作

```java
@Async
public CompletableFuture<String> getFirstName() {
	log.info("start get first name");
	try {
		Thread.sleep(2000);
	} catch (InterruptedException e) {
		throw new RuntimeException(e);
	}
	return CompletableFuture.completedFuture("006");
}

@Async
public CompletableFuture<String> getLastName() {
	log.info("start get last name");
	try {
		Thread.sleep(4000);
	} catch (InterruptedException e) {
		throw new RuntimeException(e);
	}
	return CompletableFuture.completedFuture("007");
}
```

`CompletableFuture` 是处理异步编程非常强大的工具，我们应该在合适的时机优先使用