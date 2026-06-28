# Promise

## 概念

是一个容器，里面保存着某个未来才会结束的事件，支持链式调用，避免了**回调地狱**

**特点**：

1. 有三种状态：pending（进行中）、fulfilled（已成功）和rejected（已失败）
2. 状态不可变，从**pending变为fulfilled**和从**pending变为rejected**这两种

## 基本语法

```js
const promise = new Promise(function(resolve, reject) {
  // ... some code

  if (/* 异步操作成功 */){
    resolve(value);
  } else {
    reject(error);
  }
});

promise.then(function(value) {
  // success
}, function(error) {
  // failure
});
```

## 原型方法

### Promise.prototype.then()

- 返回的是一个新的Promise实例，支持采用链式写法
- 前一个回调函数的结果可以作为参数传入第二个回调函数
- 前一个返回一个Promise对象，后一个回调函数会进行等待

```js
getJSON('/post/1.json')
  .then(function (post) {
    return getJSON(post.commentURL)
  })
  .then(
    function (comments) {
      console.log('resolved: ', comments)
    },
    function (err) {
      console.log('rejected: ', err)
    }
  )
```

### Promise.prototype.catch()

- then(null\undefined, rejection) 的别名
- 可以捕获then()方法指定的回调函数抛出的错误
- Promise 错误具有“冒泡”性质，会向后传递直到被捕获

```js
getJSON('/posts.json')
  .then(function (posts) {
    // ...
  })
  .catch(function (error) {
    // 处理 getJSON 和 前一个回调函数运行时发生的错误
    console.log('发生错误！', error)
  })
```

### Promise.prototype.finally()

- 不管 Promise 对象最后状态如何，都会执行
- 与状态无关的，不依赖于 Promise 的执行结果

```js
promise
.then(result => {···})
.catch(error => {···})
.finally(() => {···});
```

## 静态方法

### Promise.all()

- 多个 Promise的状态都变成fulfilled，返回结果数组
- 要是有一个 rejected，返回被rejected的promise的返回值

```js
const p1 = new Promise((resolve, reject) => {
  resolve('hello')
})
  .then(result => result)
  .catch(e => e)

const p2 = new Promise((resolve, reject) => {
  throw new Error('报错了')
})
  .then(result => result)
  .catch(e => e)

Promise.all([p1, p2])
  .then(result => console.log(result))
  .catch(e => console.log(e))
// ["hello", Error: 报错了] p2 有自己的catch，这个会返回一个promise，捕获错误成功后，状态会变为fulfilled。所以Promise.all走的then
```

### Promise.race()

```js
const p = Promise.race([p1, p2, p3])
```

- 有一个实例改变状态，p的状态就跟着改变

### Promise.allSettled()

确定一组异步操作是否都结束了，只有等到参数数组的所有 Promise 对象都发生状态变更（不管是fulfilled还是rejected），返回的 Promise 对象才会发生状态变更

```js
const promises = [fetch('/api-1'), fetch('/api-2'), fetch('/api-3')]

await Promise.allSettled(promises)

// 结果
// 异步操作成功时
{status: 'fulfilled', value: value}

// 异步操作失败时
{status: 'rejected', reason: reason}
```

### Promise.any()

接受一组 Promise 实例作为参数，包装成一个新的 Promise 实例返回

```js
Promise.any([
  fetch('https://v8.dev/').then(() => 'home'),
  fetch('https://v8.dev/blog').then(() => 'blog'),
  fetch('https://v8.dev/docs').then(() => 'docs')
])
  .then(first => {
    // 只要有一个 fetch() 请求成功
    console.log(first)
  })
  .catch(error => {
    // 所有三个 fetch() 全部请求失败
    console.log(error)
  })
```

- 只要参数实例有一个变成fulfilled状态，包装实例就会变成fulfilled状态
- 如果所有参数实例都变成rejected状态，包装实例就会变成rejected状态

### Promise.resolve()

将现有对象转为 Promise 对象

（1）参数是一个 Promise 实例，直接返回这个实例
（2）参数是一个 thenable 对象，会将这个对象转为 Promise 对象，然后就立即执行 thenable 对象的 then() 方法

```js
let thenable = {
  then: function (resolve, reject) {
    resolve(42)
  }
}

let p1 = Promise.resolve(thenable)
p1.then(function (value) {
  console.log(value) // 42
})
```

（3）参数不是具有then()方法的对象，或根本就不是对象

```js
const p = Promise.resolve('Hello')

p.then(function (s) {
  console.log(s)
})
// Hello
```

（4）不带有任何参数，直接返回一个resolved状态的 Promise 对象

### Promise.reject()

返回一个新的 Promise 实例，该实例的状态为rejected

```js
Promise.reject('出错了').catch(e => {
  console.log(e === '出错了')
})
```

## 应用场景

### 加载图片

```js
const preloadImage = function (path) {
  return new Promise(function (resolve, reject) {
    const image = new Image()
    image.onload = resolve
    image.onerror = reject
    image.src = path
  })
}
```

### Generator 函数与 Promise 的结合

```js
function getFoo() {
  return new Promise(function (resolve, reject) {
    resolve('foo')
  })
}

const g = function* () {
  try {
    const foo = yield getFoo()
    console.log(foo)
  } catch (e) {
    console.log(e)
  }
}

function run(generator) {
  const it = generator()

  function go(result) {
    if (result.done) return result.value

    return result.value.then(
      function (value) {
        return go(it.next(value))
      },
      function (error) {
        return go(it.throw(error))
      }
    )
  }

  go(it.next())
}

run(g)
```
