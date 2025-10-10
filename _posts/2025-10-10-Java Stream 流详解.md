---
layout: post
catalog: true
tags:
  - Java
---

# 1. Java Lambda 表达式

在了解Java Stream 流前先学习Java Lambda 表达式

Lambda 表达式是 Java 中一个非常强大和方便的语言特性，尤其在处理集合和流时。Lambda 表达式支持函数式编程范式，它允许开发人员传递行为而不是具体的值，这简化了代码结构和流程控制。

## 1.1. 基础概念

Lambda 表达式可以用来表示匿名函数，即一段没有声明的方法或者没有名字的代码片段。基本的 Lambda 表达式语法如下：

```java
(parameters) -> expression
```

或者，具有多条语句的：

```java
(parameters) -> { statements; }
```

例如，对于一个简单的求和操作，可以这样表示：

```java
(int a, int b) -> a + b
```

## 1.2. Lambda 表达式的特性

- 无需声明参数类型：编译器可以根据上下文推断参数类型。
- 适用于函数接口：函数接口是只包含一个抽象方法的接口。
- 更高效的集合操作：结合 Java 的 Stream API 使用，能更高效地处理集合。

## 1.3. Stream API 中的 Lambda 表达式

Lambda 表达式在 `Stream API` 中得到了广泛的应用，尤其在集合的操作中。以下是一个简单的例子：

```java
import java.util.Arrays;
import java.util.List;

List<String> list = Arrays.asList("Apple", "Banana", "Cherry");
list.stream()
    .filter(s -> s.startsWith("A"))
    .forEach(System.out::println);
```

在这个例子中，我们使用 Lambda 过滤出以 "A" 开头的元素，并打印出来。

## 1.4. 最佳实践

- **简洁性与可读性**：保持 Lambda 表达式的简洁性，尽可能地让代码易于阅读。
- **避免滥用**：在复杂的逻辑或多重分支情况下，将 Lambda 拆分为具名方法可能更为合适。
- **合理使用函数接口**：尽量使用 Java 提供的函数接口，避免创建不必要的接口。
- **重构现有代码**：逐步将 Lambda 表达式引入现有代码库中有助于改善代码质量。
- **谨慎处理空值**：在使用 Stream API 时，确保处理空集合的情况，以避免运行时错误。

# 2. Java Stream

**Java Stream API** 的引入，彻底改变了集合操作的方式 —— 它提供了一种**声明式、链式、可并行**的编程模型，让你的代码更简洁、更高效、更易读。

## 2.1. 什么是 Java Stream

`Stream` 是 Java 8 引入的一个**函数式编程接口**，它不是集合本身，而是对集合（如 `List`、`Set`）或数组的**函数式封装**，用于进行数据的**筛选、映射、聚合等操作**。

**Stream 的核心特性：**

|特性|描述|
|---|---|
|不存储数据|Stream 只是数据源的视图，不保存数据本身|
|不可重复使用|一个 Stream 只能使用一次，之后会抛出异常|
|支持链式调用|多个操作可以以声明式方式串联|
|支持串行与并行|可以使用 `parallelStream()` 提高性能|
|基于函数式接口|支持 Lambda 表达式，如 `Predicate`、`Function`、`Consumer` 等|
## 2.2. Stream 的生命周期

一个完整的 Stream 操作流程包括三个阶段：

1、**获取源（Source）**

2、**中间操作（Intermediate Operations）**

3、**终端操作（Terminal Operation）**


```java
// 示例：Stream 的完整流程
List<String> filtered = list.stream()
// 中间操作
    .filter(s -> s.startsWith("A"))    // 中间操作
    .map(String::toUpperCase)         // 中间操作
    .limit(5)   
  // 终端操作                    
    .toList();                      
```

## 2.3. Stream 的创建方式

|创建方式|示例|
|---|---|
|从集合创建|`list.stream()`|
|从数组创建|`Arrays.stream(array)`|
|从值创建|`Stream.of("A", "B", "C")`|
|从文件行创建|`Files.lines(Paths.get("file.txt"))`|
|无限流创建|`Stream.iterate(0, n -> n + 1)`、`Stream.generate(Math::random)`|
## 2.4. 常用中间操作（Intermediate Operations）

|操作|描述|示例|
|---|---|---|
|`filter(Predicate)`|过滤符合条件的元素|`stream.filter(s -> s.length() > 3)`|
|`map(Function)`|转换每个元素|`stream.map(String::toUpperCase)`|
|`flatMap(Function)`|扁平化处理，适用于嵌套结构|`stream.flatMap(List::stream)`|
|`distinct()`|去重（基于 `equals()` 和 `hashCode()`）|`stream.distinct()`|
|`sorted()`|排序（自然排序或自定义排序）|`stream.sorted(Comparator.reverseOrder())`|
|`limit(n)`|截取前 n 个元素|`stream.limit(10)`|
|`skip(n)`|跳过前 n 个元素|`stream.skip(5)`|

## 2.5. 常用终端操作（Terminal Operations）

|操作|描述|示例|
|---|---|---|
|`forEach(Consumer)`|遍历每个元素|`stream.forEach(System.out::println)`|
|`collect(Collector)`|收集结果（转为 List、Set、Map 等）|`stream.collect(Collectors.toList())`|
|`count()`|返回元素个数|`stream.count()`|
|`anyMatch(Predicate)`|是否有任意一个匹配|`stream.anyMatch(s -> s.contains("Java"))`|
|`allMatch(Predicate)`|是否全部匹配|`stream.allMatch(s -> s.length() > 3)`|
|`noneMatch(Predicate)`|是否没有匹配|`stream.noneMatch(s -> s.isEmpty())`|
|`findFirst()`|获取第一个元素|`stream.findFirst()`|
|`reduce(BinaryOperator)`|聚合操作（如求和）|`stream.reduce((a, b) -> a + b)`|
## 2.6. Stream 的实战应用场景

### 2.6.1. 场景1：数据过滤与转换（最常见用途）

```java
List<String> filtered = list.stream()
// 过滤出以"A"开头的字符串
        .filter(s -> s.startsWith("A"))  
// 将过滤后的字符串转为大写
        .map(String::toUpperCase)        
 // 收集结果为 List<String>
        .toList();                      
```
### 2.6.2. 场景2：统计与聚合（如求和、平均值）

```java
int sum = numbers.stream()
    .mapToInt(Integer::intValue)
    .sum();

double avg = numbers.stream()
    .mapToDouble(Integer::doubleValue)
    .average()
    .orElse(0);
```

### 2.6.3. 场景3：去重与排序

```java
List<String> uniqueSorted = list.stream()
    .distinct()
    .sorted()
    .toList();
```

### 2.6.4. 分组统计（Group By）

```java
Map<String, List<User>> grouped = users.stream()
    .collect(Collectors.groupingBy(User::getRole));
```

### 2.6.5. 多条件分组（Group By 多字段）

```java
Map<String, Map<Integer, List<User>>> grouped = users.stream()
    .collect(Collectors.groupingBy(User::getCity,
             Collectors.groupingBy(User::getAge)));
```
### 2.6.6. 场景6：分区（Partition By 条件）

```java
Map<Boolean, List<User>> partitioned = users.stream()
    .collect(Collectors.partitioningBy(u -> u.getAge() > 18));
```

### 2.6.7. 场景7：合并多个集合

```java
List<String> merged = Stream.of(list1, list2, list3)
    .flatMap(List::stream)
    .distinct()
    .toList();
```

### 2.6.8. 场景8：并行流提升性能（大数据量推荐）

```java
int sum = numbers.parallelStream()
    .mapToInt(Integer::intValue)
    .sum();
```
## 2.7. 常见误区与注意事项

|误区|正确做法|
|---|---|
|在 Stream 中修改原始集合|Stream 是只读的，不能直接修改源数据|
|多次使用同一个 Stream|Stream 只能使用一次，重复使用会抛出异常|
|忘记关闭流式资源（如 IO）|使用完文件流等资源应关闭|
|忽略空值处理（如 `findFirst()`）|使用 `Optional` 判断是否存在|
|忽略并行流的线程安全问题|并行流不适合有状态操作|
|忽略中间操作的惰性求值|中间操作不会立即执行，只有终端操作才会触发|

## 2.8. 总结：Java Stream 核心知识点一览表

|内容|说明|
|---|---|
|接口定义|`java.util.stream.Stream`|
|创建方式|从集合、数组、值、生成器等|
|中间操作|filter、map、flatMap、distinct、sorted、limit、skip|
|终端操作|forEach、collect、count、anyMatch、reduce|
|常用收集器|toList、toSet、groupingBy、partitioningBy|
|并行流|`parallelStream()`|
|应用场景|数据过滤、转换、分组、统计、聚合|
|注意事项|不可重复使用、不能修改源数据、避免中间操作副作用|
## 2.9. 附录：Stream 常用技巧速查表

|技巧|示例|
|---|---|
|转为 List|`stream.toList()`|
|转为 Set|`stream.collect(Collectors.toSet())`|
|转为 Map|`stream.collect(Collectors.toMap(User::getId, Function.identity()))`|
|获取最大最小值|`stream.max(Comparator.naturalOrder())`|
|判断是否包含|`stream.anyMatch(s -> s.equals("Java"))`|
|使用 Stream 合并字符串|`stream.collect(Collectors.joining(","))`|
|使用 Stream 生成范围数字|`IntStream.range(1, 10).boxed().toList()`|
|使用 Stream 合并多个集合|`Stream.of(list1, list2).flatMap(List::stream).toList()`|
|使用 Stream 处理 Optional|`stream.flatMap(s -> Optional.ofNullable(s))`|
|使用 Stream 处理异常|`stream.filter(s -> { try { ... } catch (...) { return false; } })`|
