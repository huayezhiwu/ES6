目录：</br>

[1. let const](#1-let-const)</br>
[2.变量的解构赋值](#2变量的解构赋值)</br>
[]()</br>
[]()</br>
[]()</br>
[]()</br>
[]()</br>
[]()</br>
[]()</br>
[]()</br>
# 1. let const
* [let命令基本用法](#let命令基本用法)</br>
* [块级作用域](#块级作用域)</br>
* [const命令](#const命令)</br>
* [顶层对象](#顶层对象)</br>
## let命令基本用法

* let声明的变量只在let命令所在的代码块内有效，且不存在变量提升

* for循环中，设置循环变量是一个父作用域，循环体内部是一个单独的子循环变量

* let存在暂时性死区，只要块级作用域存在let，它所声明的变量就绑定这个区域，不再受外部影响。<br>

```javascript

var tmp = 123;
if(true) {
    tmp = 'abc'; // ReferenceError
    let tmp;
}
```
暂时性死区意味着typeof也不再是100%安全的操作

```javascript
typeof x;  //ReferenceError
let x;


let y = y;  // ReferenceError: y is not defined

function bar(a = b, b = 2) {
    return [a, b];
}
bar(); // 参数b尚未声明，报错
```

* let不允许在相同作用域内声明同一个变量,不能在函数内部重新声明参数

```javascript
// 报错
function func() {
    let a = 10;
    var a = 1;
}

// 报错
function func() {
    let a = 10;
    let a = 1;
}
function func(arg) {
  let arg; // 报错
}

function func(arg) {
  {
    let arg; // 不报错
  }
}.
```

## 块级作用域
1.为什么需要块级作用域
* 内层变量可能会覆盖外层变量
```javascript
var tmp = new Date();

function f() {
  console.log(tmp);
  if (false) {
    var tmp = 'hello world';
  }
}

f(); // undefined
//内层变量tmp覆盖全局变量

```
* 用来记数的循环变量泄露为全局变量，如for循环。

2.es6块级作用域
* let新增块级作用域，允许块级作用域做任意嵌套，且外层无法读取内层作用域的变量
```javascript
function f1() {
  let n = 5;
  if (true) {
    let m = 10;
    let n = 10;
  }
  console.log(n); // 5   外层n不受内层重新声明的变量n的影响
  console.log(m); //报错，无法读取内层作用域的变量
}
```

* 块级作用域可替换之前广泛使用的函数立即执行表达式（IIFE）

```javascript
// IIFE 写法
(function () {
  var tmp = ...;
  ...
}());

// 块级作用域写法
{
  let tmp = ...;
  ...
}
```

* ES5规定，函数只能在顶层作用域或函数作用域中声明，不能在块级作用域之中声明，但是浏览器为了兼容旧代码，还是支持在块级作用域中声明。
```javascript
// 情况一
if (true) {
  function f() {}
}

// 情况二
try {
  function f() {}
} catch(e) {
  // ...
}
//这两种情况都能运行，不会报错
```

* ES6明确规定可在块级作用域中声明函数（函数声明类似于let）。但是如果按此规则，将无法兼容旧代码，故ES6在[附录](http://www.ecma-international.org/ecma-262/6.0/index.html#sec-block-level-function-declarations-web-legacy-compatibility-semantics)中规定，浏览器的实现允许不遵守该规定,可以有自己的[行为](https://stackoverflow.com/questions/31419897/what-are-the-precise-semantics-of-block-level-functions-in-es6),即 允许在块级作用域内声明函数；函数声明类似`var`,即会提升到全局作用域或函数作用域的头部，同时会提升到其所在块级作用域的头部

```javascript
function f() { console.log('I am outside!'); }

(function () {
  if (false) {
    // 重复声明一次函数f
    function f() { console.log('I am inside!'); }
  }
  f();
}());

//在ES5环境中，输出 “I am outside!”, 函数f提升到函数头部
//在ES6环境中，报错。代码等同于：
function f() { console.log('I am outside!'); }
(function () {
  var f = undefined;
  if (false) {
    function f() { console.log('I am inside!'); }
  }
  f();
}());
// Uncaught TypeError: f is not a function

```
注：声明函数的规则是必须在{}内部声明；考虑到环境导致的行为差异较大，尽量避免在块级作用域中声明函数。如必须在块级作用域中声明函数，请使用函数表达式，而不是函数声明语句
```javascript
// 函数声明语句
{
  let a = 'secret';
  function f() {
    return a;
  }
}

// 函数表达式
{
  let a = 'secret';
  let f = function () {
    return a;
  };
}
```

## const命令
* const声明一个只读的常量，即声明后不可修改，且声明后需立即初始化
* const同let一样，只在声明的作用域有效，变量不可提升，存在暂时性死区，且不可重复声明

```javascript
const PI = 3.1415;
PI // 3.1415

PI = 3;
// TypeError: Assignment to constant variable.

const foo;
// SyntaxError: Missing initializer in const declaration

var message = "Hello!";
let age = 25;

// 以下两行都会报错
const message = "Goodbye!";
const age = 30;

```

const所指的不可修改，不是指值的不可修改，而是指该变量指向的内存地址不可修改。对于简单型数据（数值、布尔、字符串），值就保存在变量所指向的内存地址，等同于常量。而对于复合型数据（如对象、数组）来说，const只保证其指针不变，其值可以随意变化

```javascript
const foo = {};

// 为 foo 添加一个属性，可以成功
foo.prop = 123;
foo.prop // 123

// 将 foo 指向另一个对象，就会报错
foo = {}; // TypeError: "foo" is read-only
```
若真想将对象完全冻结，可使用Object.freeze(obj)方法，冻结后不可以修改该对象的属性与值。（冻结分为浅冻结与深冻结，具体[点击此处](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze)）

至此可知，ES6声明变量的方法共有6种，`var`, `function`, `let`,`const`, `import`, `class`。其中`var`, `function`是ES5仅有的两种方法。

## 顶层对象
顶层对象，在浏览器环境中指的是`window`, 在Node环境中指的是`global`，在浏览器和web worker环境中指的是`self`。在ES5中顶层对象等同于全局变量，在ES6中规定，`var`、 `function`声明的全局变量依然有顶层对象的属性，而`let`、`const`声明的全局变量不具有顶层对象的属性。

```javascript
var a = 1;
// 如果在 Node 的 REPL 环境，可以写成 global.a
// 或者采用通用方法，写成 this.a
window.a // 1

let b = 1;
window.b // undefined
```
由于顶层对象获取困难，现有垫片库[system.global](https://github.com/ljharb/System.global)可在所有环境中都可以拿到顶层对象global

```javascript
// CommonJS 的写法
require('system.global/shim')();

// ES6 模块的写法
import shim from 'system.global/shim'; shim();
```

# 2.变量的解构赋值
* [数组的解构赋值](#数组的解构赋值)</br>
* [对象的解构赋值](#对象的解构赋值)</br>
* [字符串的解构赋值](#字符串的解构赋值)</br>
* [数值和布尔值的解构赋值](#数值和布尔值的解构赋值)</br>
* [函数参数解构赋值](#函数参数解构赋值)</br>
* [圆括号问题](#圆括号问题)</br>
* [用途](#用途)</br>

## 数组的解构赋值



## 对象的解构赋值



## 字符串的解构赋值
字符串也可以被解构，它被解构成了一种类似数组的对象。类似数组的对象都有一个`length`属性，故可以对它解构赋值。

```javascript

const [a, b, c, d, e] = 'hello';
a // "h"
b // "e"
c // "l"
d // "l"
e // "o"

let {length : len} = 'hello';
len // 5
```
## 数值和布尔值的解构赋值
解构赋值时，若等号右边不是对象与数组，都会先将其转化为对象。由于`undefined`、`null`无法转化为对象，故解构失败，报错。
数值和布尔值拥有对象属性`toString`

```javascript
let {toString: s} = 123;
s === Number.prototype.toString // true

let {toString: s} = true;
s === Boolean.prototype.toString // true

//上面代码中，数值和布尔值的包装对象都有toString属性，因此变量s都能取到值。

let { prop: x } = undefined; // TypeError
let { prop: y } = null; // TypeError

```

## 函数参数解构赋值
函数参数的解构也会有默认值
```javascript
function move({x, y} = { x: 0, y: 0 }) {
  return [x, y];
}

move({x: 3, y: 8}); // [3, 8]
move({x: 3}); // [3, undefined]
move({}); // [undefined, undefined]
move(); // [0, 0]

//此时是为函数move的参数指定默认值，若传入参数，默认值会被直接替代
```

## 圆括号问题
ES6规定：只要有可能导致解构的歧义，就不能使用圆括号。
* 不能使用圆括号的情况：
- 变量声明语句
- 函数参数（也属于变量声明）
- 赋值语句的模式

```javascript
// 全部报错
let [(a)] = [1];
let {x: (c)} = {};
let ({x: c}) = {};
let {(x: c)} = {};
let {(x): c} = {};

let { o: ({ p: p }) } = { o: { p: 2 } };
//上面 6 个语句都会报错，因为它们都是变量声明语句，模式不能使用圆括号。

// 报错
function f([(z)]) { return z; }
// 报错
function f([z,(x)]) { return x; }

// 全部报错
({ p: a }) = { p: 42 };
([a]) = [5];
//将整个模式放在圆括号中，导致报错

// 报错
[({ p: a }), { x: c }] = [{}, {}];
//将部分模式放在圆括号中，导致报错
```
* 可以使用圆括号的情况只有一种，即赋值语句的非模式部分

```javascript
[(b)] = [3]; // 正确
({ p: (d) } = {}); // 正确
[(parseInt.prop)] = [3]; // 正确
```

## 用途

* 交换变量的值
```javascript
let x = 1;
let y = 2;
[x, y] = [y, x];
```

* 从函数返回多个值（之前想要函数返回多个值，只能将它们放在对象或数组中返回）
```javascript
// 返回一个数组
function example() {
  return [1, 2, 3];
}
let [a, b, c] = example();

// 返回一个对象
function example() {
  return {
    foo: 1,
    bar: 2
  };
}
let { foo, bar } = example();
```

* 函数参数的定义
```javascript
// 参数是一组有次序的值
function f([x, y, z]) { ... }
f([1, 2, 3]);

// 参数是一组无次序的值
function f({x, y, z}) { ... }
f({z: 3, y: 2, x: 1});
```

* 提取JSON数据
```javascript
let jsonData = {
  id: 42,
  status: "OK",
  data: [867, 5309]
};

let { id, status, data: number } = jsonData;

console.log(id, status, number);
// 42, "OK", [867, 5309]
```

* 函数参数的默认值
```javascript
jQuery.ajax = function (url, {
  async = true,
  beforeSend = function () {},
  cache = true,
  complete = function () {},
  crossDomain = false,
  global = true,
  // ... more config
} = {}) {
  // ... do stuff
};
```

* 遍历Map结构
```javascript
const map = new Map();
map.set('first', 'hello');
map.set('second', 'world');

for (let [key, value] of map) {
  console.log(key + " is " + value);
}
// first is hello
// second is world

// 获取键名
for (let [key] of map) {
  // ...
}

// 获取键值
for (let [,value] of map) {
  // ...
}
```

* 输入模块的指定方法
```javascript
const { SourceMapConsumer, SourceNode } = require("source-map");
```

