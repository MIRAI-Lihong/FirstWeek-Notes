# module

## export

规定模块的对外接口

```js
// profile.js
let firstName = 'Michael'

function multiply(x, y) {
  return x * y
}

export {firstName, multiply}
```

- 能够导出**函数， 类，var、let、const 声明的变量**
- `export`语句输出的接口，与其对应的值是**动态绑定**关系
- `export`命令可以出现在模块的任何位置，只要处于**模块顶层**就可以

## import

通过`import`命令加载`export`命令定义的模块的对外接口

```js
// main.js
import {firstName, lastName as surname, year} from './profile.js'

function setName(element) {
  element.textContent = firstName + ' ' + surname
}
```

- 可以使用`as`关键字重命名
- import命令输入的变量都是**只读**的

## 模块的整体加载

```js
import * as circle from './circle'
```

## export default

```js
// export-default.js
export default function () {
  console.log('foo')
}

// import-default.js
// 可以为该匿名函数指定任意名字
import customName from './export-default'
customName() // 'foo'
```

> 一个模块只能有一个默认输出，export default命令只能使用一次

## import 属性

允许为 import 命令设置属性，主要用于导入非模块的代码，比如 JSON 数据、WebAssembly 代码、CSS 代码。目前，**只支持导入 JSON 数据**

```js
const configData = await import('./config-data.json', {with: {type: 'json'}})
```

## import()

**动态加载模块**

```js
const main = document.querySelector('main')

import(`./section-modules/${someVariable}.js`)
  .then(module => {
    module.loadPageInto(main)
  })
  .catch(err => {
    main.textContent = err.message
  })
```

- 可以用在任何地方，不仅仅是模块，非模块的脚本也可以使用
- **运行时执行，动态加载，异步加载**

## import.meta

返回当前模块的**元信息**

1. import.meta.url 返回**当前模块的 URL 路径**
2. import.meta.scriptElement 返回加载模块的那个`<script>`元素

> 只能在模块内部使用，如果在模块外部使用会报错
> Node.js 环境中，import.meta.url返回的总是本地路径，即file:URL协议的字符串，比如file:///home/user/foo.js
