## 1. let const
#### let命令

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

    typeof x;  //ReferenceError
    let x;
    

    let y = y;  // ReferenceError: y is not defined

    function bar(a = b, b = 2) {
        return [a, b];
    }
    bar(); // 参数b尚未声明，报错

let不允许在相同作用域内声明同一个变量
