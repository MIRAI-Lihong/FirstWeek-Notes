# async

## 用法

```js
// 函数声明
async function foo() {}

// 函数表达式
const foo = async function () {};

// 对象的方法
let obj = { async foo() {} };
obj.foo().then(...)

// 箭头函数
const foo = async () => {};
```

## 语法

### 返回 Promise 对象

```js
async function f() {
  return 'hello world'
}

f().then(v => console.log(v))
// "hello world"
```

> 当内部的异步操作执行完后，才会发生状态改变

```js
async function getTitle(url) {
  let response = await fetch(url)
  let html = await response.text()
  return html.match(/<title>([\s\S]+)<\/title>/i)[1]
}
getTitle('https://tc39.github.io/ecma262/').then(console.log)
// "ECMAScript 2017 Language Specification" 异步操作执行完后，才会打印
```

## await

### 基本语法

```js
async function f() {
  // 等同于
  // return 123;
  return await 123
}

f().then(v => console.log(v))
// 123
```

```js
class Sleep {
  constructor(timeout) {
    this.timeout = timeout
  }
  then(resolve, reject) {
    const startTime = Date.now()
    setTimeout(() => resolve(Date.now() - startTime), this.timeout)
  }
}

;(async () => {
  const sleepTime = await new Sleep(1000)
  console.log(sleepTime)
})()
```

### 错误处理

如果await后面的异步操作出错，那么等同于async函数返回的 Promise 对象被reject

```js
async function f() {
  await new Promise(function (resolve, reject) {
    throw new Error('出错了')
  })
}

f()
  .then(v => console.log(v))
  .catch(e => console.log(e))
// Error：出错了
```

> 将其放在try...catch代码块之中防止出错

### 注意点

（1）把`await`命令放在`try...catch`代码块中

```js
async function myFunction() {
  try {
    await somethingThatReturnsAPromise()
  } catch (err) {
    console.log(err)
  }
}
```

（2）多个await命令后面的异步操作，如果不存在继发关系，最好让它们同时触发

```js
let [foo, bar] = await Promise.all([getFoo(), getBar()])
```

（3）await命令只能用在async函数之中
（4）async 函数可以保留运行堆栈
