# Class 的继承

## 基本概念

ES6 通过 `extends` 关键字实现类的继承

```js
class Point {}

class ColorPoint extends Point {
  constructor(x, y, color) {
    super(x, y) // 调用父类的 constructor
    this.color = color
  }

  toString() {
    return this.color + ' ' + super.toString() // 调用父类的 toString()
  }
}
```

## super()

### 子类必须调用 super()

子类的 `constructor()` 中必须调用 `super()`，否则新建实例时会报错

```js
class ColorPoint extends Point {
  constructor() {
    // 没有调用 super()
  }
}

let cp = new ColorPoint()
```

### super() 之后才能用 this

```js
class ColorPoint extends Point {
  constructor(x, y, color) {
    this.color = color // super() 之前不能用 this
    super(x, y)
    this.color = color
  }
}
```

### 父类构造函数一定先执行

```js
class Foo {
  constructor() {
    console.log(1)
  }
}

class Bar extends Foo {
  constructor() {
    super()
    console.log(2)
  }
}

new Bar()
// 1
// 2
```

## 私有属性和私有方法的继承

父类的**私有属性/方法**（`#` 开头）**不会被子类继承**

```js
class Foo {
  #p = 1
  #m() {
    console.log('hello')
  }
}

class Bar extends Foo {
  constructor() {
    super()
    console.log(this.#p)
    this.#m()
  }
}
```

如果需要访问，父类可以提供公共读写方法：

```js
class Foo {
  #p = 1
  getP() {
    return this.#p
  }
}

class Bar extends Foo {
  constructor() {
    super()
    console.log(this.getP())
  }
}
```

---

## 静态属性和静态方法的继承

父类的静态属性/方法也会被子类继承：

```js
class A {
  static hello() {
    console.log('hello world')
  }
}

class B extends A {}

B.hello() // hello world
```

### 静态属性是浅拷贝继承

1. **值类型（number、string 等）：子类修改不影响父类**

```js
class A {
  static foo = 100
}
class B extends A {
  constructor() {
    super()
    B.foo--
  }
}
new B()
B.foo // 99
A.foo // 100
```

2. **引用类型（对象、数组等）**：子类和父类指向同一个对象，**互相影响**

```js
class A {
  static foo = {n: 100}
}
class B extends A {
  constructor() {
    super()
    B.foo.n--
  }
}
new B()
B.foo.n // 99
A.foo.n // 99 被影响
```

## Object.getPrototypeOf()

用于获取子类的父类，也可以用来判断继承关系：

```js
Object.getPrototypeOf(ColorPoint) === Point // true
```

---

## super 关键字详解

### 作为函数使用 — 代表父类构造函数

- 只能在子类的 `constructor()` 中调用
- 调用后**生成继承父类的 `this` 对象**
- `super()` 内部的 `this` 指向**子类实例**，相当于 `A.prototype.constructor.call(this)`

```js
class A {
  constructor() {
    console.log(new.target.name)
  }
}

class B extends A {
  constructor() {
    super()
  }
}

new A() // "A"
new B() // "B"
```

> **注意**：`super()` 执行时子类属性还未绑定到 `this`，若存在同名属性，拿到的是父类的值

```js
class A {
  name = 'A'
  constructor() {
    console.log('My name is ' + this.name)
  }
}

class B extends A {
  name = 'B'
}

new B()
```

### 作为对象使用 — 指向父类原型对象

在**普通方法**中，`super` 指向**父类的原型对象**

```js
class A {
  p() {
    return 2
  }
}

class B extends A {
  constructor() {
    super()
    console.log(super.p())
  }
}
```

- 父类实例属性（定义在 `this` 上的）无法通过 `super` 访问

在**静态方法**中，`super` 指向**父类本身**

```js
class Parent {
  static myMethod(msg) {
    console.log('static', msg)
  }
}

class Child extends Parent {
  static myMethod(msg) {
    super.myMethod(msg) // 调用 Parent.myMethod
  }
}

Child.myMethod(1) // "static 1"
```

## 七、类的 prototype 和 **proto**

```js
子类.__proto__ === 父类
子类.prototype.__proto__ === 父类.prototype
```

```js
class A {}
class B extends A {}

B.__proto__ === A // true
B.prototype.__proto__ === A.prototype // true
```
