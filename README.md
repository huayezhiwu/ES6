目录：
[1.let const](#1-let-const)

## 1. let const
#### let命令基本用法

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

#### 块级作用域
##### 为什么需要块级作用域
1. 内层变量可能会覆盖外层变量
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
2.用来记数的循环变量泄露为全局变量

##### es6块级作用域
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

* ES6明确规定可在块级作用域中声明函数（函数声明类似于let）。但是如果按此规则，将无法兼容旧代码，故ES6在[附录](http://www.ecma-international.org/ecma-262/6.0/index.html#sec-block-level-function-declarations-web-legacy-compatibility-semantics)中规定，浏览器的实现允许不遵守该规定,可以有自己的[行为](https://stackoverflow.com/questions/31419897/what-are-the-precise-semantics-of-block-level-functions-in-es6)。
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
//在ES6环境中，报错

```









