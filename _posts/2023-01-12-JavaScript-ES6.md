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

**在HTML中添加JavaScript代码的方式**

内部的

```html
<script>内容</script>
```

外部的

```html
<script src="name.js"></script>
```

# 4. 语言特性

* 不区分整型和浮点型
* 语句结尾加  ;
* 注释符和c语言一样
* 浏览器的console调试JS：输出信息console.log()
* 查看变量类型：typeof(变量)
* 字符串可用 + 连接
* JavaScript语句区分大小写

# 5. 作用域

```
* 全局作用域：函数外
* 局部作用域：内
* 变量提升：在代码执行前变量已经在编译阶段被声明了
```
# 6. 变量

命名规则：不能数字开头、不能以保留字命名、驼峰式命名法（第一个字母应小写，此后每一个单词中的第一个字母当为大写）

`let`、`const`和`var`都是JavaScript中用于声明变量的关键字，但它们的行为和作用域规则有所不同。

**var**

这是最早的JavaScript变量声明方式，它的作用域是函数级的。如果在函数外部声明，它将成为全局变量。`var`声明的变量会被提升到它们所在的函数或全局作用域的顶部。在es6的语法中，已经不建议使用

**let**

这是ES6引入的新的变量声明方式，所声明的变量，只在`let`命令所在的代码块内有效（一个花括号内为一个代码块）

**const**

这也是ES6引入的，用于声明常量的关键字，它的作用域也是块级的。一旦被赋值，就不能改变。

在现代JavaScript开发中，建议尽可能使用`let`和`const`，因为它们的块级作用域规则更易于理解和管理。使用`const`可以明确表示变量的值不应该改变，这可以帮助防止在代码中意外地修改这个值。当你需要一个可以改变的变量时，使用`let`。尽量避免使用`var`，除非你有特定的理由需要函数级作用域或变量提升。

## 6.1. 数据类型

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

## 6.2. 运算符
算术运算符：加、减、乘、`/` 除、`%` 取余

关系运算符：`==`、`===`恒等、`!=`、`>`、`<`、`>=`、`<=`

赋值运算符：`=`、`+=`、`-=`、`*=`、`/=`、`%=`

一元运算符：a++、++a

逻辑运算符：逻辑与 `&&`、逻辑或 `||`、逻辑非 `！`（`&&`优先于`||`）

# 7. 数组遍历与属性

由于 `for in` 循环会枚举原型链上的所有属性，唯一过滤这些属性的方式是使用 hasOwnProperty 函数， 因此会比普通的 `for` 循环慢上好多倍。
## 7.1. 遍历

为了达到遍历数组的最佳性能，推荐使用经典的 for 循环。

```JavaScript
var list = [1, 2, 3, 4, 5, ...... 100000000];
for(var i = 0, l = list.length; i < l; i++) {
    console.log(list[i]);
}
```
上面代码有一个处理，就是通过 l = list.length 来缓存数组的长度。

虽然 length 是数组的一个属性，但是在每次循环中访问它还是有性能开销。

## 7.2. `length` 属性

`length` 属性的 _getter_ 方式会简单的返回数组的长度，而 _setter_ 方式会**截断**数组。

```JavaScript
var foo = [1, 2, 3, 4, 5, 6]; 
foo.length = 3; 
foo; // [1, 2, 3]
```
## 7.3. `Array` 构造函数

由于 `Array` 的构造函数在如何处理参数时有点模棱两可，因此总是推荐使用数组的字面语法 `[]`  来创建数组
# 8. 函数

函数是 JavaScript 中的一种对象，这意味着可以把函数像值一样传递。 一个常见的用法是把匿名函数作为回调函数传递到异步函数中。

## 8.1. 函数声明

```JavaScript
function foo() {}
```
上面的方法会在执行前被 解析 (hoisted)，因此它存在于当前上下文的任意一个地方， 即使在函数定义体的上面被调用也是对的。

```JavaScript
foo(); // 正常运行，因为foo在代码运行前已经被创建
function foo() {}
```

## 8.2. 匿名函数赋值表达式

把一个匿名的函数赋值给变量 `foo`

```JavaScript
var foo = function() {};
```
错误方式
```JavaScript
foo; // 'undefined'
foo(); // 出错：TypeError
var foo = function() {};
```
由于 var 定义了一个声明语句，对变量 foo 的解析是在代码运行之前，因此 foo 变量在代码运行时已经被定义过了。

但是由于赋值语句只在运行时执行，因此在相应代码执行之前， foo 的值缺省为 undefined。
## 8.3. `this` 的工作原理

JavaScript 有一套完全不同于其它语言的对 `this` 的处理机制。 在**五**种不同的情况下 ，`this` 指向的各不相同

**全局范围内**

当在全部范围内使用 `this`，它将会指向全局对象。浏览器中运行的 JavaScript 脚本，这个全局对象是 `window`。

**函数调用**

```JavaScript
foo();
```

这里 `this` 也会指向_全局_对象。

> **ES5 注意:** 在严格模式下（strict mode），不存在全局变量。 这种情况下 `this` 将会是 `undefined`。

**方法调用**

```JavaScript
test.foo(); 
```

这个例子中，`this` 指向 `test` 对象

**调用构造函数**

```JavaScript
new foo(); 
```

如果函数倾向于和 `new` 关键词一块使用，则我们称这个函数是 构造函数。 在函数内部，`this` 指向新创建的对象。

**显式的设置 `this`**

```JavaScript
function foo(a, b, c) {}

var bar = {};
foo.apply(bar, [1, 2, 3]); // 数组将会被扩展，如下所示
foo.call(bar, 1, 2, 3); // 传递到foo的参数是：a = 1, b = 2, c = 3
```

当使用 Function.prototype 上的 call 或者 apply 方法时，函数内的 this 将会被 显式设置为函数调用的第一个参数。

因此函数调用的规则在上例中已经不适用了，在 foo 函数内 this 被设置成了 bar。

## 8.4. 闭包和引用

闭包是 JavaScript 一个非常重要的特性，这意味着当前作用域**总是**能够访问外部作用域中的变量。 因为 函数 是 JavaScript 中唯一拥有自身作用域的结构，因此闭包的创建依赖于函数。

## 8.5. `arguments` 对象

JavaScript 中每个函数内都能访问一个特别变量 `arguments`。这个变量维护着所有传递到这个函数中的参数列表

注意: 由于 arguments 已经被定义为函数内的一个变量。 因此通过 var 关键字定义 arguments 或者将 arguments 声明为一个形式参数， 都将导致原生的 arguments 不会被创建。

arguments 变量不是一个数组（Array），实际上它是一个对象（Object）。因此，无法对 arguments 变量使用标准的数组方法

**转化为数组**

```JavaScript
Array.prototype.slice.call(arguments);
```
**自动更新**

`arguments` 对象为其内部属性以及函数形式参数创建 _getter_ 和 _setter_ 方法

因此，改变形参的值会影响到 `arguments` 对象的值，反之亦然

**ES5 提示:** 这些 _getters_ 和 _setters_ 在严格模式下（strict mode）不会被创建。

## 8.6. 作用域与命名空间

尽管 JavaScript 支持一对花括号创建的代码段，但是并不支持块级作用域； 而仅仅支持 _函数作用域_

```JavaScript
function test() { // 一个作用域
    for(var i = 0; i < 10; i++) { // 不是一个作用域
        // count
    }
    console.log(i); // 10
}
```

**隐式的全局变量**

```JavaScript
// 脚本 A
foo = '42';

// 脚本 B
var foo = '42'
```
脚本 A 在全局作用域内定义了变量 `foo`，而脚本 B 在当前作用域内定义变量 `foo`。

再次强调，上面的效果**完全不同**，不使用 `var` 声明变量将会导致隐式的全局变量产生

```JavaScript
// 全局作用域

var foo = 42;

function test() {

    // 局部作用域

    foo = 21;

}

test();

console.log(foo); // 21
```

在函数 `test` 内不使用 `var` 关键字声明 `foo` 变量将会覆盖外部的同名变量。 起初这看起来并不是大问题，但是当有成千上万行代码时，不使用 `var` 声明变量将会带来难以跟踪的 BUG

**局部变量**

JavaScript 中局部变量只可能通过两种方式声明，一个是作为函数参数，另一个是通过 `var` 关键字声明

```JavaScript
// 全局变量
var foo = 1;
var bar = 2;
var i = 2;

function test(i) {
    // 函数 test 内的局部作用域
    i = 5;

    var foo = 3;
    bar = 4;
}
test(10);
```

`foo` 和 `i` 是函数 `test` 内的局部变量，而对 `bar` 的赋值将会覆盖全局作用域内的同名变量。

**名称解析顺序**

当访问函数内的 foo 变量时，JavaScript 会按照下面顺序查找：

1. 当前作用域内是否有 var foo 的定义。
2. 函数形式参数是否有使用 foo 名称的。
3. 函数自身是否叫做 foo。
4. 回溯到上一级作用域，然后从 `#1` 重新开始。

**命名空间**

推荐使用匿名包装器（也就是自执行的匿名函数）来创建命名空间。这样不仅可以防止命名冲突， 而且有利于程序的模块化。

```JavaScript
( // 小括号内的函数首先被执行
function() {}
) // 并且返回函数对象
() // 调用上面的执行结果，也就是函数对象
```
有一些其他的调用函数表达式的方法，比如下面的两种方式语法不同，但是效果一模一样。
```JavaScript
// 另外两种方式
+function(){}();
(function(){}());
```
# 9. 对象和属性

JavaScript 中所有变量都可以当作对象使用，除了两个例外 null 和 undefined

一个常见的误解是数字的字面值（literal）不能当作对象使用。这是因为 JavaScript 解析器的一个错误， 它试图将点操作符解析为浮点数字面值的一部分。有很多变通方法可以让数字的字面值看起来像对象

```JavaScript
2..toString(); // 第二个点号可以正常解析
2 .toString(); // 注意点号前面的空格
(2).toString(); // 2先被计算
```

## 9.1. 对象作为数据类型

JavaScript 的对象可以作为哈希表使用，主要用来保存命名的键与值的对应关系

使用对象的字面语法  `{}`  可以创建一个简单对象，没有任何自定义属性

```JavaScript
var foo = {}; // 一个空对象

// 一个新对象，拥有一个值为12的自定义属性'test'
var bar = {test: 12}; 
```
## 9.2. 访问属性

有两种方式来访问对象的属性，点操作符或者中括号操作符

```JavaScript
var foo = {name: 'kitten'}
foo.name; // kitten
foo['name']; // kitten

var get = 'name';
foo[get]; // kitten

foo.1234; // SyntaxError
foo['1234']; // works
```

两种语法是等价的，点操作符是推荐做法，但是中括号操作符在下面两种情况下依然有效

- 动态设置属性
- 属性名不是一个有效的变量名（比如属性名中包含空格，或者属性名是 JS 的关键词）

## 9.3. 删除属性

删除属性的唯一方法是使用 `delete` 操作符；设置属性为 `undefined` 或者 `null` 并不能真正的删除属性， 而**仅仅**是移除了属性和值的关联

```JavaScript
var obj = {
    bar: 1,
    foo: 2,
    baz: 3
};
obj.bar = undefined;
obj.foo = null;
delete obj.baz;

for(var i in obj) {
    if (obj.hasOwnProperty(i)) {
        console.log(i, '' + obj[i]);
    }
}
```

## 9.4. 属性名的语法

对象的属性名可以使用字符串或者普通字符声明

```JavaScript
var test = {
    'case': 'I am a keyword so I must be notated as a string',
    delete: 'I am a keyword too so me' // 出错：SyntaxError
};
```
上面的第二种声明方式在 ECMAScript 5 之前会抛出 SyntaxError 的错误。

这个错误的原因是 delete 是 JavaScript 语言的一个关键词；因此为了在更低版本的 JavaScript 引擎下也能正常运行， 必须使用字符串字面值声明方式。
## 9.5. 原型

在写复杂的 JavaScript 应用之前，充分理解原型链继承的工作方式是每个 JavaScript 程序员必修的功课。 要提防原型链过长带来的性能问题，并知道如何通过缩短原型链来提高性能。 更进一步，绝对不要扩展内置类型的原型，除非是为了和新的 JavaScript 引擎兼容。
## 9.6. `for in` 循环

**对象的遍历**

```javascript
for(键 in 对象){console.log(对象名[键]);}
```
## 9.7. 常用封装对象

**数学对象Math**

取绝对值 `.abs()`、取随机数 `.random()`、向下取整`.rnd()`

**时间对象Date**

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

**数组对象`[]`**

获取数组长度` .length`

插入元素 `.push(元素)`

删除最后一个元素` .pop()`

**字符串对象**

```javascript
var  n = 'JavaScript'
```

将字符串全转为小写 `.toLowerCase()`

将字符串全转为大写 `.toUpperCase()`

# 10. 事件响应
事件为异步事件

## 10.1. 何为事件

用户鼠标的点击，键盘某个按键的点击

## 10.2. 基本交互方法
输出

```javascript
document.write(内容)
```

信息对话框：alert(内容)，接收到的是字符串值

选择对话框：confirm(内容)，接收一个参数，并转为字符串显示，返回一个值，true或false

显示提示的对话框：prompt(提示文本内容，文本输入框为默认文本)，接收两个参数，第二个参数可不填，只返回一个值，用户点击取消时，返回null

# 11. ES6 新特性

**扩展运算符`...`**

可在函数调用/数组构造时，将数组表达式或者string在语法层面展开。还以在构造对象时，将对象表达式按key-value的方式展开

将之前的内容全部展开，再生成一个新的对象

```JavaScript
list: ['周一','周二']
[...list]
```