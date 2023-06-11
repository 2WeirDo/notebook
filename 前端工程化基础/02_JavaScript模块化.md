## 1. 什么是模块化？

- 事实上模块化开发最终的目的是将程序划分成一个个小的结构；
- 这个结构中编写属于自己的逻辑代码，有自己的作用域，定义变量名词时不会影响到其他的结构；
- 这个结构可以将自己希望暴露的变量、函数、对象等导出给其结构使用；
- 也可以通过某种方式，导入另外结构中的变量、函数、对象等；

◼ 上面说提到的结构，就是模块；按照这种结构划分开发程序的过程，就是模块化开发的过程；



## 2. 没有模块化带来的问题

◼ 早期没有模块化带来了很多的问题：比如**命名冲突的问题**

◼ 当然，我们有办法可以解决上面的问题：**立即函数调用表达式（**`**IIFE**`**）**

`IIFE` (Immediately Invoked Function Expression)

◼ 但是，我们其实带来了新的**问题**：

- 第一，我必须**记得每一个模块中返回对象的命名**，才能在其他模块使用过程中正确的使用；
- 第二，代码写起来**混乱不堪**，每个文件中的代码都需要包裹在一个匿名函数中来编写；
- 第三，在没有合适的规范情况下，每个人、每个公司都可能会任意命名、甚至**出现模块名称相同的情况**；

◼ 所以，我们会发现，虽然实现了模块化，但是我们的实现过于简单，并且是**没有规范**的。



## 3. CommonJS规范和Node关系

- `**Node**`**中对**`**CommonJS**`**进行了支持和实现**，让我们在开发node的过程中可以方便的进行模块化开发：

- - 在Node中**每一个js文件都是一个单独的模块**；
  - 这个模块中包括CommonJS规范的**核心变量：**`**exports**`**、**`**module.exports**`**、**`**require**`；
  - 我们可以使用这些变量来方便的进行模块化开发；

- 前面我们提到过模块化的核心是**导出和导入**，Node中对其进行了实现：

- - exports和module.exports可以负责对模块中的内容进行导出；
  - require函数可以帮助我们导入其他模块（自定义模块、系统模块、第三方库模块）中的内容；



## 4. exports导出



```javascript
const UTIL_NAME = "util_name"

function formatCount() {
  return "200万"
}

function formatDate() {
  return "2022-10-10"
}

console.log(exports) // {}

exports.UTIL_NAME = UTIL_NAME
exports.formatCount = formatCount
exports.formatDate = formatDate
```





## 5. module.exports导出

**Node导出的本质是在导出module.exports对象**



◼ 但是Node中我们经常导出东西的时候，又是通过module.exports导出的：

-  module.exports和exports有什么关系或者区别呢？

◼ 我们追根溯源，通过维基百科中对CommonJS规范的解析：

-  CommonJS中是没有module.exports的概念的；
-  但是为了实现模块的导出，Node中使用的是Module的类，每一个模块都是Module的一个实例，也就是module；
-  所以在Node中真正用于导出的其实根本不是exports，而是module.exports；
-  因为module才是导出的真正实现者；

◼ 但是，为什么exports也可以导出呢？

-  这是因为module对象的exports属性是exports对象的一个引用；
-  也就是说 module.exports = exports = main中的bar；



```javascript
// 1.在开发中使用的很少
exports.name = name
exports.age = age
exports.sayHello = sayHello

// 2.将模块中内容导出
// 结论: Node导出的本质是在导出module.exports对象
module.exports.name = name
module.exports.age = age
module.exports.sayHello = sayHello

console.log(exports === module.exports)
// true

// 3.开发中常见的写法
// 对象增强写法
module.exports = { 
  name,
  age,
  sayHello
}

// exports.name = "哈哈哈哈"
// module.exports.name = "哈哈哈哈"
// 以上两句一个意思
```



![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1684255414374-3ded3071-358e-426b-b80e-9acc36041206.png)



**如果module.exports不再引用exports对象了，那么修改exports还有意义吗？**

已经没有意义

## 6. require导入

本质上就是一个引用赋值

```javascript
1.直接获取导出的对象, 从对象中获取属性
const util = require("./util.js")

console.log(util.UTIL_NAME)
console.log(util.formatCount())
console.log(util.formatDate())

2.导入对象之后, 直接对其进行解构
const { 
  UTIL_NAME,
  formatCount, 
  formatDate 
} = require("./util.js")

console.log(UTIL_NAME)
console.log(formatCount())
console.log(formatDate())
```



## 7. require导入细节

**require的查找规则是怎么样的呢？**

 这里我总结比较常见的查找规则：

 导入格式如下：`require(X)`



◼ **情况一**：X是一个Node核心模块，比如path、http

 直接返回核心模块，并且停止查找



◼ 情况二：X是以 ./ 或 ../ 或 /（根目录）开头的

◼ 第一步：将X当做一个文件在对应的目录下查找；

 1.如果有后缀名，按照后缀名的格式查找对应的文件

 2.如果没有后缀名，会按照如下顺序：

✓ 1> 直接查找文件X

✓ 2> 查找X.js文件

✓ 3> 查找X.json文件

✓ 4> 查找X.node文件

◼ 第二步：没有找到对应的文件，将X作为一个目录

- 查找目录下面的index文件

✓ 1> 查找X/index.js文件

✓ 2> 查找X/index.json文件

✓ 3> 查找X/index.node文件

◼ 如果没有找到，那么报错：not found



◼ 情况三：直接是一个X（没有路径），并且X不是一个核心模块

◼ /Users/coderwhy/Desktop/Node/TestCode/04_learn_node/05_javascript-module/02_commonjs/main.js中编写 require('why’)

由上至下查找

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1684256069637-93878efc-c79b-4bef-bfc0-61f72c523c30.png)

◼ 如果上面的路径中都没有找到，那么报错：not found









## 8. 模块的加载过程

◼ 结论一：模块在被第一次引入时，模块中的js代码会被运行一次

◼ 结论二：模块被多次引入时，会缓存，最终只加载（运行）一次

-  为什么只会加载运行一次呢？
-  这是因为每个模块对象**module都有一个属性：loaded**。
-  为`false`表示还没有加载，为`true`表示已经加载；

引入一次模块后会将 loaded 设置为 true, 表示已经加载

◼ 结论三：如果有循环引入，那么加载顺序是什么？

◼ 如果出现右图模块的引用关系，那么加载顺序是什么呢？

-  这个其实是一种数据结构：图结构；
-  图结构在遍历的过程中，有深度优先搜索（DFS, depth first search）和广度优先搜索（BFS, breadth first search）；
-  `Node`采用的是**深度优先算法**：main -> aaa -> ccc -> ddd -> eee ->bbb

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1684256657260-f104dbdb-afc2-4641-b0ff-d6570b924f48.png)

## 9. CommonJS规范缺点

◼ `CommonJS`加载模块是**同步**的：

-  同步的意味着只有等到对应的模块加载完毕，当前模块中的内容才能被运行**；**
-  这个在服务器不会有什么问题，因为服务器加载的js文件都是本地文件，加载速度非常快；

◼ 如果将它应用于浏览器呢？

-  浏览器加载js文件需要先从服务器将文件下载下来，之后再加载运行；
-  那么采用同步的就意味着后续的js代码都无法正常运行，即使是一些简单的DOM操作；

◼ 所以在浏览器中，我们通常不使用CommonJS规范：

-  当然在`webpack`中使用`CommonJS`是另外一回事； 
-  因为它会将我们的代码转成浏览器可以直接执行的代码；

◼ 在早期为了可以在浏览器中使用模块化，通常会采用AMD或CMD：

-  但是目前一方面现代的浏览器已经支持ES Modules，另一方面借助于webpack等工具可以实现对CommonJS或者ES Module代码的转换；
-  AMD和CMD已经使用非常少了；



## 10. 认识 ES Module



◼ ES Module和CommonJS的模块化有一些不同之处：

-  一方面它使用了import和export关键字；
-  另一方面它采用编译期的静态分析，并且也加入了动态引用的方式；

◼ ES Module模块采用export和import关键字来实现模块化：

-  `export`负责将模块内的内容导出；
-  `import`负责从其他模块导入内容；

◼ 了解：采用ES Module将自动采用严格模式：use strict



## 11. 案例代码结构组件

```javascript
<script src="./modules/foo.js" type="module"></script>
<script src="main.js" type="module"></script>
```

◼ 如果直接在浏览器中运行代码，会报如下错误：

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1684257159684-ad5276c3-c2a2-418a-a240-c1d7348d6f36.png)

在我们打开对应的html时, 如果html中有使用模块化的代码, 那么必须开启一个服务来打开 

- 你需要注意本地测试 — 如果你通过本地加载Html 文件 (比如一个 file:// 路径的文件), 你将会遇到 CORS 错误，因为Javascript 模块安全性需要；
- 你需要通过一个服务器来测试；

◼ 我这里使用的VSCode**插件**：Live Server  可以解决这个问题





## 12. export关键字

注意不是exports , 不要和CommonJS导出搞混淆了

◼ export关键字将一个模块中的变量、函数、类等导出；

◼ 我们希望将其他中内容全部导出，它可以有如下的方式：

◼ 方式一：在语句声明的前面直接加上export关键字

◼ 方式二：将所有需要导出的标识符，放到export后面的 {}中

-  注意：这里的 {}里面不是ES6的对象字面量的增强写法，{}也不是表示一个对象的；
-  所以： export {name: name}，是错误的写法；

◼ 方式三：导出时给标识符起一个别名 

一般不用, 导入起别名倒是可以

-  通过`as`关键字起别名



```javascript
// 3.导出方式三:
export const name = "why"
export const age = 18

export function sayHello() {
  console.log("sayHello")
}

export class Person {}

// console.log(name)

// 1.导出方式一: 
export {
  name,
  age,
  sayHello
}

// 2.导出方式二: 导出时给标识符起一个别名
export {
  name as fname,
  age,
  sayHello
}
```

## 13. import关键字

◼ import关键字负责从另外一个模块中导入内容

◼ 导入内容的方式也有多种：

◼ 方式一：import {标识符列表} from '模块'；

-  注意：这里的{}也不是一个对象，里面只是存放导入的标识符列表内容；

◼ 方式二：导入时给标识符起别名

-  通过as关键字起别名

◼ 方式三：通过 * 将模块功能放到一个模块功能对象（a module object）上

```javascript
// 1.导入方式一: 
import { name, age, sayHello } from "./foo.js"

// 2.导入方式二: 导入时给标识符起别名
import { name as fname, age, sayHello } from "./foo.js"

// 3.导入时可以给整个模块起别名
import * as foo from "./foo.js"

const name = "main"

console.log(name)
console.log(foo.name)
console.log(foo.age)
foo.sayHello()
```



## 14. 导入导出的优化

将所需模块合并到一个 index.js 中, 方便 main.js 一次性导入

```javascript
export function formatCount() {
  return "200万"
}

export function formatDate() {
  return "2022-11-11"
}
export function parseLyric(lyricString) {
  return ["歌词"]
}
import { formatCount, formatDate } from './format.js'
import { parseLyric } from './parse.js'

export {
  formatCount,
  formatDate,
  parseLyric
}

// 优化一:
// export { formatCount, formatDate } from './format.js'
// export { parseLyric } from './parse.js'

// 优化二:
// export * from './format.js'
// export * from './parse.js'
// import { formatCount, formatDate } from "./utils/format.js"
// import { parseLyric } from "./utils/parse.js"

import { 
  formatCount, 
  formatDate, 
  parseLyric 
} from './utils/index.js'

console.log(formatCount())
console.log(formatDate())

console.log(parseLyric())
```

## 15. export和import结合使用

◼ 补充：export和import可以结合使用

先导入再导出

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1684257434685-580c2eca-0358-4018-a1c7-a5e6f95006a4.png)

◼ 为什么要这样做呢？

 在开发和封装一个功能库时，通常我们希望将暴露的所有接口放到一个文件中；

 这样方便指定统一的接口规范，也方便阅读；

 这个时候，我们就可以使用export和import结合使用；



## 16. default用法

◼ 前面我们学习的导出功能都是有名字的导出（named exports）：

-  在导出export时指定了名字；
-  在导入import时需要知道具体的名字；

◼ 还有一种导出叫做默认导出（default export）

-  默认导出export时可以不需要指定名字；
-  在导入时不需要使用 {}，并且可以自己来指定名字；
-  它也方便我们和现有的CommonJS等规范相互操作；

◼ 注意：在一个模块中，只能有一个默认导出（default export）；



```javascript
function parseLyric() {
  return ["歌词"]
}

const name = "aaaa"

// export {
//   parseLyric,
//   name
// }

// 1.默认导出
export default parseLyric

// 2.定义标识符直接作为默认导出
export default function () {
  return ["新歌词"];
}


// 注意事项: 一个模块只能有一个默认导出
// import { parseLyric } from "./parse_lyric.js"

import parseLyric from "./parse_lyric.js"
// 这里的parseLyric是任意变量都可以
console.log(parseLyric())
```

## 17. import函数

◼ 通过import加载一个模块，是不可以在其放到逻辑代码中的，比如：

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1684257517290-4e99258f-908b-4f9a-8cc0-fce074d384f1.png)

◼ 为什么会出现这个情况呢？

-  这是因为ES Module在被JS引擎解析时，就必须知道它的依赖关系；
-  由于这个时候js代码没有任何的运行，所以无法在进行类似于if判断中根据代码的执行情况；
-  甚至拼接路径的写法也是错误的：因为我们必须到运行时能确定path的值；

路径不能拼写

◼ 但是某些情况下，我们确确实实希望动态的来加载某一个模块：

-  如果根据不懂的条件，动态来选择加载模块的路径；
-  这个时候我们需要使用 import() 函数来动态加载；

✓ import函数返回一个`Promise`，可以通过then获取结果；

```javascript
export const name = "foo"
export const age = 18

export function sayHello() {
  console.log("sayHello")
}

console.log(import.meta)
// import函数的使用
let flag = true
if (flag) {
  
  // 不允许在逻辑代码中编写import导入声明语法, 只能写到js代码顶层
  import { name, age, sayHello } from "./foo.js" 
  console.log(name, age)
  // 会报错

  // 如果确实是逻辑成立时, 才需要导入某个模块
  // import函数
  const importPromise = import("./foo.js")
  importPromise.then(res => {
    console.log(res.name, res.age)
  })
  
  // 简便写法
  import("./foo.js").then(res => {
    console.log(res.name, res.age)
  })

}
```



## 18. import meta

◼ import.meta是一个给JavaScript模块暴露特定上下文的元数据属性的对象。

-  它包含了这个模块的信息，比如说这个模块的URL；
-  在ES11（ES2020）中新增的特性；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1684259559566-b8c13e6f-24d5-46a5-9588-cfe58676ba71.png)

## 19. ES Module的解析流程

◼ ES Module是如何被浏览器解析并且让模块之间可以相互引用的呢？

-  https://hacks.mozilla.org/2018/03/es-modules-a-cartoon-deep-dive/

◼ ES Module的解析过程可以划分为三个阶段：

-  阶段一：构建（Construction），根据地址查找js文件，并且下载，将其解析成模块记录（Module Record）；
-  阶段二：实例化（Instantiation），对模块记录进行实例化，并且分配内存空间，解析模块的导入和导出语句，把模块指向对应的内存地址。
-  阶段三：运行（Evaluation），运行代码，计算值，并且将值填充到内存地址中；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1684257815532-f42a5f2b-98ef-4669-8071-bd4e9065fd56.png)



### 19.1. 阶段一：构建阶段



![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1684257842721-a6c4920e-6c77-4671-a2ef-043808613852.png)



### 19.2. 阶段二和三：实例化阶段 – 求值阶段

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1684257885255-44fbfb72-9bc3-4c11-b7b2-b276692c7f3f.png)







### 19.3. ES Module是如何被浏览器解析并且让模块之间可以相互引用的呢？

ES Module 在浏览器中的解析和模块之间的相互引用可以通过以下阶段来说明：



1.  下载阶段（Fetching）：当浏览器遇到`<script type="module">`标签或在 JavaScript 代码中使用`import`语句时，它会开始下载指定的模块文件。浏览器会根据指定的 URL 发出网络请求，获取模块文件。 
2.  解析阶段（Parsing）：一旦模块文件被下载，浏览器会对其进行解析。在解析过程中，浏览器会检查模块文件中的依赖关系，并发起进一步的下载请求，以获取这些依赖的模块文件。这些依赖关系通过`import`语句或动态`import()`函数来定义。 
3.  编译阶段（Compilation）：在解析阶段之后，浏览器将模块文件转换为可执行的代码。这包括将模块中的导入和导出语句转换为内部的模块加载和导出逻辑。 
4.  执行阶段（Execution）：一旦编译完成，浏览器会按照模块之间的依赖关系执行它们。每个模块的顶层作用域被视为独立的模块作用域，模块内部的变量和函数默认不会污染全局作用域。 



在执行阶段，当模块需要导入其他模块时，浏览器会检查模块的依赖关系，并确保被导入的模块已经加载和执行。如果模块之间存在循环依赖，浏览器会根据特定的算法来解决循环依赖问题。



ES Module 的加载和解析过程是异步的，这意味着模块文件可以按需进行加载，并且不会阻塞其他代码的执行。这使得浏览器能够在下载和解析模块的同时，继续渲染页面或执行其他 JavaScript 代码，从而提高性能和响应性。



需要注意的是，为了避免跨域安全问题，ES Module 的加载遵循同源策略，模块文件必须与当前页面具有相同的协议、主机和端口，或者服务器端允许跨域访问。