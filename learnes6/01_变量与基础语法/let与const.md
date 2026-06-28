# let与const.md

## let

与 var 类似，都是声明变量的方式，但是 let 只在其所在的块级作用域有效
**语法及区别**

```js
{
  let a = 1
  var b = 1
}
console.log(a, b)
```

### 变量提升

var 声明的变量可以在声明前访问，值为 **undefined**
let 改变了这个语法，在声明前访问会报错，称为“**暂时性死区**”

### 重复声明

let 不允许在同一作用域声明同名变量，var允许

### 块级作用域

**解决问题**：让私有变量的方式更为简单，之前是采用IIFE，现在直接let

```js
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

**函数声明**：

- 允许在块级作用域内声明函数。
- 函数声明类似于var，即会提升到全局作用域或函数作用域的头部。
- 同时，函数声明还会提升到所在的块级作用域的头部。

## const

声明一个常量，值**不能改变**，且必须**初始化**。只在声明所在的**块级作用域**有效，存在暂时性死区，**不可重复声明**

```js
const PI = 3.1415
PI = 3 // Uncaught TypeError: Assignment to constant variable.
```

> var 命令和 function 命令声明的全局变量，依旧**是**顶层对象的属性；let 命令、const 命令、class 命令声明的全局变量，**不属于**顶层对象的属性。
> **浏览器**里面，顶层对象是 **window** ，但 Node 和 Web Worker 没有 window。
> 浏览器和 **Web Worker** 里面， **self** 也指向顶层对象，但是 Node 没有self。
> **Node** 里面，顶层对象是 **global** ，但其他环境都不支持。
