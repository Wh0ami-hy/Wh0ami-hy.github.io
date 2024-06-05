---
layout: post   	
catalog: true 	
tags:
    - JavaScript
---




# 1. 前言

ECMAScript 6.0（以下简称 ES6）。ECMAScript 和 JavaScript 的关系是，前者是后者的规格，后者是前者的一种实现。

# 2. 组成

 Javascript 由三部分构成，ECMAScript，DOM和BOM。根据浏览器的不同，具体的表现形式也不尽相同

- ECMAScript：描述了JS的语法和基本对象
- DOM 是文档对象模型，DOM是为方便js操作html的，看起来就是html标签，js中通过document.get(...)获取dom节点的对象，再继续操作
- BOM 是浏览器对象模型，是用于操作浏览器而出现的API

# 3. 添加方式

内部的

```html
<script>内容</script>
```

外部的

```html
<script src="name.js"></script>
```

内联的

```html
<button onclick="createParagraph()"> Click me</button>
```

# 4. 特性

* 不区分整型和浮点型
* 语句结尾加  ;
* 注释符和c语言一样
* 浏览器的console调试JS：输出信息console.log()
* 查看变量类型：typeof(变量)
* 字符串可用 + 连接
* JavaScript语句区分大小写
* 不允许访问本地硬盘，并且不能将数据存到服务器上

# 5. 基础语法

## 5.1. 变量

命名规则：不能数字开头、不能以保留字命名、驼峰式命名法（第一个字母应小写，此后每一个单词中的第一个字母当为大写）

`let`、`const`和`var`都是JavaScript中用于声明变量的关键字，但它们的行为和作用域规则有所不同。

**var**

这是最早的JavaScript变量声明方式，它的作用域是函数级的。如果在函数外部声明，它将成为全局变量。`var`声明的变量会被提升到它们所在的函数或全局作用域的顶部。在es6的语法中，已经不建议使用

**let**

这是ES6引入的新的变量声明方式，所声明的变量，只在`let`命令所在的代码块内有效（一个花括号内为一个代码块）

**const**

这也是ES6引入的，用于声明常量的关键字，它的作用域也是块级的。一旦被赋值，就不能改变。

在现代JavaScript开发中，建议尽可能使用`let`和`const`，因为它们的块级作用域规则更易于理解和管理。使用`const`可以明确表示变量的值不应该改变，这可以帮助防止在代码中意外地修改这个值。当你需要一个可以改变的变量时，使用`let`。尽量避免使用`var`，除非你有特定的理由需要函数级作用域或变量提升。

## 5.2. 数据类型
**基础数据类型**

```
String字符串

Number数值型

Boolean布尔型

Null空值：使用typeof检测类型时，输出object型

Undefined未定义型
```

**引用数据类型**

Object对象

**数据类型的转换**

```
parseInt()：转为整型

parseFloat()：转为浮点型

String()：转为字符串型

Boolean()：转换为布尔型，转为false（0、空），其余都会转为true
```

## 5.3. 运算符
算术运算符：加、减、乘、`/` 除、`%` 取余

关系运算符：`==`、`===`恒等、`!=`、`>`、`<`、`>=`、`<=`

赋值运算符：`=`、`+=`、`-=`、`*=`、`/=`、`%=`

一元运算符：a++、++a

逻辑运算符：逻辑与 `&&`、逻辑或 `||`、逻辑非 `！`（`&&`优先于`||`）

## 5.4. 流程控制
语法基本与c语言一致

```
* 条件语句
* switch ...case语句
* 循环结构：while、do while、for
```

## 5.5. 数组
与c语言有些不同

可以存放任何类型的数据

多维数组：支持数组中存放数组

定义方式

```javascript
var arr[1,2,'hello']
```

构造函数方式

```javascript
var a1 = new Array(1,2,'hello')
```

操作：获取数组长度`.length`方法

## 5.6. 函数
**声明方式**

关键字声明

```javascript
function 函数名(){.....}
```

表达式声明

```javascript
var 变量名 = function(){......}
```

**匿名函数**

```javascript
var 变量名 = function(){......}
```

**立即执行函数**（自调用匿名函数）：防止全局变量污染，封装一个局部作用

```javascript
(function(){......})()
```

函数可被当做参数，被传入另一个函数中

函数可被当做参数，return返回

## 5.7. 作用域
```
* 全局作用域：函数外
* 局部作用域：内
* 变量提升：在代码执行前变量已经在编译阶段被声明了
```

## 5.8. 对象

**属性**（特征）

**方法**（行为）

**声明**

字面量声明
```javascript
var obj = {};
```

对象中的数据都是以键值对的形式存在的，如果值是函数则称为方法

```javascript
var obj = {age:12,high:180,name:'哈哈',fly:function(){}}
```

实例化方式(内置构造函数)

```javascript
var obj = new Object()
```

实例化自定义构造函数方式
```javascript
function fun(){}
var f = new fun()
```

**获取对象的属性或方法**

`对象.属性名`

在对象内部获取对象的属性或方法

`this.属性名`

**this**

永远指向一个对象（this运行在哪个对象下，就指向哪个对象）

**对象的遍历**

```javascript
for(键 in 对象){console.log(对象名[键]);}
```

**对象属性的删除**

```
delete  对象名.属性名
```

**包装对象**

原始类型的数据在一定条件下自动转为对象

**数学对象**

Math：取绝对值 `.abs()`、取随机数 `.random()`、向下取整`.rnd()`

**时间对象**

Date

JS中获取的时间是计算本地时间

实例化构造函数获取时间对象

```javascript
var da = new Date()
```

获取小时 `.getHours()`

获取年 `.getFullYear()`

获取月份   `.getMonth()`

JS中月份的数组是从0开始的

获取日期 `.getDate()`

**数组对象**

获取数组长度` .length`

插入元素 `.push(元素)`

删除最后一个元素` .pop()`

**字符串对象**

```javascript
var  n = 'JavaScript'
```

将字符串全转为小写 `.toLowerCase()`

将字符串全转为大写 `.toUpperCase()`

# 6. 事件响应
事件为异步事件

## 6.1. 何为事件

用户鼠标的点击，键盘某个按键的点击

## 6.2. 基本交互方法
输出

```javascript
document.write(内容)
```

信息对话框：alert(内容)，接收到的是字符串值

选择对话框：confirm(内容)，接收一个参数，并转为字符串显示，返回一个值，true或false

显示提示的对话框：prompt(提示文本内容，文本输入框为默认文本)，接收两个参数，第二个参数可不填，只返回一个值，用户点击取消时，返回null

# 7. ES6 新特性

**扩展运算符`...`**

可在函数调用/数组构造时，将数组表达式或者string在语法层面展开。还以在构造对象时，将对象表达式按key-value的方式展开

将之前的内容全部展开，再生成一个新的对象

```JavaScript
list: ['周一','周二']
[...list]
```