---
title: import 和 require 的区别
tags:
  - ES6
  - Node.js
---

## 引言

我们在平时的前端开发中,都是用的 `import xxx from xxx` 进行模块导入.这几天笔者在学习 Koa 的开发,这里面大量涉及到 Node.js 的操作.而在 Node.js 的开发中,都是通过 `cosnt xxx = require(xxx)` 这种方式来进行模块导入.那么当前台后台在一起开发的时候,我常常把他们弄混.可能是太菜了 😢,所以我花了半天的时间去学习了解他们两者的区别.<!-- more -->

## import 属于 ES6 规范

### import

import 是在编译过程中加载,也就是说是在代码执行前执行,比如说,import 后面的路径写错了,在运行代码前就会抛错,所以在编写代码时,必须放在模块顶部(import 是静态执行的).

### import 的导入方式

```js
import foo from "./output"
import { b as B } from "./output"
import * as OBj from "./output"
import { a } from "./output"
import { b as BB } from "./output"
import c, { d } from "./output"
```

以上导入方式会在下面 👇 一一解释

### exoprt 和 export default

相对于 import(导入),则应该有导出,那就是 exoprt 和 export default.同样的,他们也是 ES6 规范.export 是导出,export dafault 是默认导出,一个模块可以有多个 export,但是只能有一个 export default,export default 可以和多个 export 共存.export default 为默认导出,导出的是用{}包裹的一个对象,以键值对的形式存在.
我们来看一下下面这个 export 例子:
导出：

```js
//ouput.js
const info = "hhhh"
export { info }
```

导入：

```js
//input.js
import { info } from "./output.js" //此处的import {info}和export {info},两个a是一一对应关系
console.log(info) //'hhhh'
```

注意,上面的 export {info}导出的 info,和 import {info}导入的 info 是同一个 info.
我们再来看看 export default 的例子:
导出：

```js
//output.js
const info = "hhhh"
export default { info }
```

导入:

```js
//input.js
import info from "./output.js" //此处的info和export default{info},不是同一个info,
console.log(info) //'hhhh'
```

那意思就是我们只是导入的是一个 info 的引用,我们也可以这样用:

```js
//input.js
import otherName from "./output.js"
console.log(otherName) //'hhhh'
```

也可以达到相同的效果.这里 import 导入的是 export default 下的对象,叫什么名字都可以,因为只会存在一个 export default.

### exoprt 和 export default 混合使用

实际上,export 和 export fault 是可以同时使用的,请看下面这个例子:

导出:

```js
//output.js
const a = "info1"
const b = "info2"
const c = "info3"
const d = "info4"
function printC() {
  console.log(`printC执行,c的值是${c}`)
}
export { a }
export { b }
export default { b, d, printC }
```

导入:

```js
//input.js
import obj, { a, b } from "./output"
console.log(a) //info1
console.log(b) //info2
console.log(obj) //{ b: 'info1', d: 'info4', printC: [Function: printC] }
```

### as 重命名

还是上面的那个例子:

导出:

```js
//output.js
const a = "info1"
const b = "info2"
const c = "info3"
const d = "info4"
function printC() {
  console.log(`printC执行,c的值是${c}`)
}
export { a }
export { b }
export default { b, d, printC }
```

导入:

```js
//input.js
  import {a as A} from './output' // √ 支持,A是将要在input.js中使用的
  import {* as A} from './output'// x 不支持
  import * as obj from './output' // √ 将模块中所有的 export 和 export default的属性一起导出
  console.log(A); //info1
  console.log(obj); //{ a: 'info1',b: 'info2',default: { b: 'info2', d: 'info4', printC: [Function: printC] } }
```

上面的 `import {a as A} from './output'` 是指将'a'导出为别名'A',在导入的模块中,你就可以使用'A'进行操作.而 `import * as obj from './output'` 是将 input.js 中的所有 export 和 export default 导出的内容组合成一个对象返回.
我们来 `console` 一下上例中 `obj` 的其他属性:

```js
//input.js
import * as obj from "./output" // √ 将模块中所有的 export 和 export default的属性一起导出
console.log(obj) // { a: 'info1',b: 'info2',default: { b: 'info2', d: 'info4', printC: [Function: printC] } }
console.log(obj.a) // info1
console.log(obj.b) // info2
obj.default.printC() // printC执行,c的值是info3
```

为什么这里可以打印出 `obj.a` 和 `obj.b` ? 那是因为,我们在 output.js 里面利用 export 将这两个属性导出了,别忘记上面那句话:`import * as obj from './output'` 是将 input.js 中的所有 export 和 export default 导出的内容组合成一个对象返回.下面来来看看 require.

## require 属于 CommonJS 规范

### require

require 是运行时调用,所以是动态执行的,所以 require 理论上可以运用在代码的任何地方.所以在性能上会比 import 差一些.
require 导入模块就没那么复杂了,导出时是什么样,导入时就还是什么样.其中,导出可以用:

```js
exports.xxx = xxx
module.exports = xxx
```

导入则用 `const xxx = require('xxx')` 即可完成导入.
下面我们来看一个例子( `module.exports` 这种导出方式):
导出:

```js
//output.js
const Name1 = "hhh1"
const Name2 = "hhh2"
module.exports = {
  Name1,
  Name2,
  foo1: function () {
    console.log("这是foo1函数!")
  },
  foo2: function () {
    console.log("这是foo2函数!")
  }
}
```

导入:

```js
// input.js
const test = require("./output.js")
console.log(test.Name1) // hhh1
console.log(test.Name2) // hhh2
test.foo1() // 这是foo1函数!
test.foo2() // 这是foo2函数!
```

我们再来看看 `exports.xxx = xxx` 这种导出方式:
导出:

```js
//output.js
const Name1 = "hhh1"
const Name2 = "hhh2"
exports.foo1 = function () {
  console.log("这是foo1函数!")
}
exports.foo2 = function () {
  console.log("这是foo1函数!")
}
exports.Name1 = Name1
exports.Name2 = Name2
```

导入:

```js
// input.js
const test = require("./output.js")
console.log(test.Name1) // hhh1
console.log(test.Name2) // hhh2
test.foo1() // 这是foo1函数!
test.foo2() // 这是foo2函数!
```

但是值得注意的是,`exports.xxx = xxx` 和 `module.exports = xxx;`一起使用时, `exports.xxx` 的属性会失效.我们来看一个例子:
导出:

```js
//output.js
const firstName = "Michael"
const lastName = "Jackson"
const year = 1958
module.exports = {
  firstName,
  lastName,
  year
}
exports.name = "hhhh"
```

导入:

```js
// input.js
const test = require("./output.js")
console.log(test.firstName) //Michael
console.log(test.lastName) //Jackson
console.log(test.year) //1958
console.log(test.name) //undefined
```

在上面的 console 中, `exports.name = 'hhhh'`的属性被 `module.exports`覆盖了,所以失效了.

## 总结

1.  require,exports,module.exports 属于 CommonJS 规范,import,export,export default 属于 ES6 规范
2.  require 支持动态导入,动态匹配路径,import 对这两者都不支持
3.  require 是运行时调用,import 是编译时调用
4.  require 是赋值过程,import 是解构过程
5.  对于 export 和 export default 不同的使用方式,import 就要采取不同的引用方式,主要区别在于是否存在{},export 导出的,import 导入需要{},导入和导出一一对应,export default 默认导出的,import 导入不需要{}
6.  exports 是 module.exports 一种简写形式,不能直接给 exports 赋值
7.  当直接给 module.exports 赋值时,exports 会失效.
