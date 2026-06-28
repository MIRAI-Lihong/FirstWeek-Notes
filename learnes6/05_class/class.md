# class 基本语法

## constructor()

`constructor()`方法是类的默认方法，如果没有显式定义，**自动添加**

```js
class Point {}

// 等同于
class Point {
  constructor() {}
}
```

```js
class Foo {
  // 默认返回实例对象（即this），可以指定返回另外一个对象
  constructor() {
    return Object.create(null)
  }
}

new Foo() instanceof Foo
// false
```

## 类的实例

使用 **`new`** 命令创建

```js
class Point {
  constructor(x, y) {
    this.x = x
    this.y = y
  }

  // 在原型上
  toString() {
    return '(' + this.x + ', ' + this.y + ')'
  }
  // 属性名可以采用表达式
  [methodName]() {
    // ...
  }
}

let point = new Point(2, 3)
```

> 1. 类的属性和方法，除非显式定义在其本身（即定义在this对象上），否则都是定义在原型上
> 2. 类的所有实例共享一个原型对象

## getter和setter

拦截属性的存取行为

```js
class MyClass {
  constructor() {
    // ...
  }
  get prop() {
    return 'getter'
  }
  set prop(value) {
    console.log('setter: ' + value)
  }
}

let inst = new MyClass()

inst.prop = 123
// setter: 123

inst.prop
// 'getter'
```

## class 表达式

与函数一样，类也可以使用表达式的形式定义

```js
const MyClass = class Me {
  getClassName() {
    // Me 只能在内部使用
    return Me.name
  }
}
// 在 Class 外部，这个类只能用MyClass引用
let inst = new MyClass()
```

## 静态方法

```js
class Foo {
  static classMethod() {
    return 'hello'
  }
}

// 类名调用
Foo.classMethod() // 'hello'
```

- 不会被实例继承，直接通过类名调用
- 父类的静态方法可以被子类继承
- 可以从super对象上调用

## 私有属性与私有方法

在属性名和方法名之前使用`#`表示。

- 只能在`类的内部`使用
- 可以设置 `getter` 和 `setter` 方法

```js
class IncreasingCounter {
  #count = 0
  get #value() {
    console.log('Getting the current value!')
    return this.#count
  }
  #increment() {
    this.#count++
  }
}
```
