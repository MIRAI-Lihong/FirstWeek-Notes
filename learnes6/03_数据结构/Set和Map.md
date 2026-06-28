# Set和Map

## 1.Set

ES6 提供了新的数据结构 Set。它类似于数组，但是成员的值都是**唯一**的，没有重复的值

**基本语法**：

```js
const s = new Set()
let arr = [1, 2, 3, 4, 4]

arr.forEach(x => s.add(x))

const set = new Set(arr)
```

**方法与属性**：
四个操作方法：

```js
const s = new Set()
s.add(1).add(2).add(2)
// 注意2被加入了两次

// 返回实例的长度
s.size // 2

s.has(1) // true
s.has(2) // true
s.has(3) // false

s.delete(2) // true
s.has(2) // false

s.clear() // 清除所有成员
```

---

四个遍历方法：

```js
let set = new Set(['red', 'green', 'blue'])

for (let item of set.keys()) {
  console.log(item)
}
// red
// green
// blue

for (let item of set.values()) {
  console.log(item)
}
// red
// green
// blue

for (let item of set.entries()) {
  console.log(item)
}
// ["red", "red"]
// ["green", "green"]
// ["blue", "blue"]

set.forEach((value, key) => console.log(key + ' : ' + value))
```

## 2.WeakSet

WeakSet 结构与 Set 类似，也是不重复的值的集合。但是，它与 Set 有两个区别。

1. WeakSet 的成员只能是**对象和 Symbol** 值，而不能是其他类型的值
2. WeakSet 中的对象都是**弱引用**

**基本语法**：

```js
// 数组的成员只能是对象
const a = [
  [1, 2],
  [3, 4]
]
const ws = new WeakSet(a)
// WeakSet {[1, 2], [3, 4]}
```

**方法与属性**：

```js
const ws = new WeakSet()
const obj = {}
const foo = {}

ws.add(window)
ws.add(obj)

ws.has(window) // true
ws.has(foo) // false

ws.delete(window) // true
ws.has(window) // false
```

## 3.Map

**基本语法**：

```js
const map = new Map([
  ['name', '张三'],
  ['title', 'Author']
])

map.size // 2
map.has('name') // true
map.get('name') // "张三"
map.has('title') // true
map.get('title') // "Author"
```

**遍历方法**：

- Map.prototype.keys()：返回键名的遍历器。
- Map.prototype.values()：返回键值的遍历器。
- Map.prototype.entries()：返回所有成员的遍历器。
- Map.prototype.forEach()：遍历 Map 的所有成员。

**其他数据结构转换**：

（1）Map 转对象

```js
function strMapToObj(strMap) {
  let obj = Object.create(null)
  for (let [k, v] of strMap) {
    obj[k] = v
  }
  return obj
}

const myMap = new Map().set('yes', true).set('no', false)
strMapToObj(myMap)
```

（2）对象转 Map

```js
let obj = {a: 1, b: 2}
let map = new Map(Object.entries(obj))
```

（3）Map 转为 JSON

```js
function strMapToJson(strMap) {
  return JSON.stringify(strMapToObj(strMap))
}

let myMap = new Map().set('yes', true).set('no', false)
strMapToJson(myMap)
// '{"yes":true,"no":false}'
```

（4）JSON 转为 Map

```js
function jsonToStrMap(jsonStr) {
  return objToStrMap(JSON.parse(jsonStr))
}

jsonToStrMap('{"yes": true, "no": false}')
// Map {'yes' => true, 'no' => false}
```

## 4.WeakMap

与Map结构类似，也是用于生成键值对的集合
与Map的区别：

1. WeakMap只接受**对象和 Symbol 值**作为键名
2. WeakMap的键名所指向的对象，不计入垃圾回收机制

**基本语法**：

```js
const wm = new WeakMap()

// size、forEach、clear 方法都不存在
// 只有四个方法可用：get()、set()、has()、delete()
```

**应用场景**：
DOM 节点作为键名

```js
let myWeakmap = new WeakMap()

myWeakmap.set(document.getElementById('logo'), {timesClicked: 0})

document.getElementById('logo').addEventListener(
  'click',
  function () {
    let logoData = myWeakmap.get(document.getElementById('logo'))
    logoData.timesClicked++
  },
  false
)
```

## 5.WeakRef

**基本语法**：
**直接创建对象的弱引用**

WeakRef 实例对象有一个`deref()`方法，**如果原始对象存在，该方法返回原始对象；如果原始对象已经被垃圾回收机制清除，该方法返回`undefined`**。

```js
let target = {}
let wr = new WeakRef(target)

let obj = wr.deref()
if (obj) {
  // target 未被垃圾回收机制清除
  // ...
}
```

**应用场景**：
缓存：

```js
function makeWeakCached(f) {
  const cache = new Map()
  return key => {
    const ref = cache.get(key)
    if (ref) {
      const cached = ref.deref()
      if (cached !== undefined) return cached
    }

    const fresh = f(key)
    cache.set(key, new WeakRef(fresh))
    return fresh
  }
}

const getImageCached = makeWeakCached(getImage)
```
