---
layout: post   	
catalog: true 	
tags:
    - Web
---





## 组成

```
* 核心(ECMAScript)
* 文档对象模型(DOM)
* 浏览器对象模型(BOM)
```

## 添加方式
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

## 特性
```
* JavaScript语句区分大小写
* 不允许访问本地硬盘，并且不能将数据存到服务器上
```

## 语法基础
**注意**

```
* 不区分整型和浮点型
* 语句结尾加  ;
* 注释符和c语言一样
* 浏览器的console调试JS：输出信息console.log()
* 查看变量类型：typeof(变量)
* 字符串可用 + 连接
```

### 变量
```
* 声明：var name
* 命名规则：不能数字开头、不能以保留字命名、驼峰式命名法（第一个字母应小写，此后每一个单词中的第一个字母当为大写）
```

### 数据类型
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

### 运算符
算术运算符：加、减、乘、`/` 除、`%` 取余

关系运算符：`==`、`===`恒等、`!=`、`>`、`<`、`>=`、`<=`

赋值运算符：`=`、`+=`、`-=`、`*=`、`/=`、`%=`

一元运算符：a++、++a

逻辑运算符：逻辑与 `&&`、逻辑或 `||`、逻辑非 `！`（`&&`优先于`||`）

### 流程控制
语法基本与c语言一致

```
* 条件语句
* switch ...case语句
* 循环结构：while、do while、for
```

### 数组
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

### 函数
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

### 作用域
```
* 全局作用域：函数外
* 局部作用域：内
* 变量提升：在代码执行前变量已经在编译阶段被声明了
```

### 对象
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

## 事件响应
事件为异步事件

### 何为事件

用户鼠标的点击，键盘某个按键的点击

### 基本交互方法
输出

```javascript
document.write(内容)
```

信息对话框：alert(内容)，接收到的是字符串值

选择对话框：confirm(内容)，接收一个参数，并转为字符串显示，返回一个值，true或false

显示提示的对话框：prompt(提示文本内容，文本输入框为默认文本)，接收两个参数，第二个参数可不填，只返回一个值，用户点击取消时，返回null
