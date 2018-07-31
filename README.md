目录：</br>

[1. let const](#1-let-const)
[* let命令基本用法](#let命令基本用法)
[* 块级作用域](#块级作用域)
[* const命令](#const命令)
[]()
[]()
[]()
# 1. let const
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
* 用来记数的循环变量泄露为全局变量

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

## const
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
若真想将对象完全冻结，可使用[Object.freeze(obj)](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze)方法，冻结后不可以修改该对象的属性与值。








