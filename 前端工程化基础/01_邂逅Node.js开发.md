## 1. Node.js是什么

◼ 官方对`Node.js`的定义：

- `Node.js`是一个基于`**V8 JavaScript**`**引擎**的`**JavaScript**`**运行时环境。**

◼ **也就是说**`**Node.js**`**基于**`**V8**`**引擎来执行**`**JavaScript**`**的代码，但是不仅仅只有V8引擎：**

- 前面我们知道`V8`可以嵌入到任何`C ++`应用程序中，无论是`Chrome`还是`Node.js`，事实上都是嵌入了`V8`引擎来执行`JavaScript`代码；
- 但是在`Chrome`浏览器中，还需要解析、渲染`HTML、CSS`等相关渲染引擎，另外还需要提供支持浏览器操作的API、浏览器自己的事件循环等；
- 另外，在`Node.js`中我们也需要进行一些额外的操作，比如文件系统读/写、网络IO、加密、压缩解压文件等操作；



## 2. 浏览器和Node.js架构区别

我们可以简单理解规划出`Node.js`和浏览器的差异 : 

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202306100056769.png)



## 3. Node.js架构

◼ 我们来看一个单独的`Node.js`的架构图：

- 我们编写的`JavaScript`代码会经过`V8`引擎，再通过`Node.js`的`Bindings`，将任务放到`Libuv`的事件循环中；
- `libuv`（Unicorn Velociraptor—独角伶盗龙）是使用`C`语言编写的库；
- `libuv`提供了事件循环、文件系统读写、网络IO、线程池等等内容；

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202306100056788.png)





## 4. Node.js的应用场景

- 应用一：目前前端开发的库都是**以**`**node**`**包的形式进行管理**；
- 应用二：`**npm、yarn、pnpm**`**工具**成为前端开发使用最多的工具；
- 应用三：越来越多的公司使用`Node.js`作为`**web**`**服务器开发、中间件、代理服务器**；
- 应用四：大量项目需要借助`Node.js`完成**前后端渲染的同构应用**；
- 应用五：资深前端工程师需要**为项目编写脚本工具**（前端工程师编写脚本通常会使用`JavaScript`，而不是`Python`或者`shell`）；
- 应用六：很多企业在使用`Electron`来**开发桌面应用程序**



## 5. Node的版本工具

```
nvm
```

 通过 `nvm install latest` 安装最新的node版本

 通过 `nvm list` 展示目前安装的所有版本

 通过 `nvm use` 切换版本



## 6. Node的REPL

◼ 什么是`REPL`呢？感觉挺高大上

- `REPL`是Read-Eval-Print Loop的简称，翻译为“读取-求值-输出”循环；
- `REPL`是一个简单的、交互式的编程环境；

◼ 事实上，我们浏览器的`console`就可以看成一个`REPL`。

◼ `Node`也给我们提供了一个`REPL`环境，我们可以在其中演练简单的代码。

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202306100056745.png)



## 7. Node程序传递参数

◼ 正常情况下执行一个`node`程序，直接跟上我们对应的文件即可：

◼ 但是，在某些情况下执行`node`程序的过程中，我们可能希望给`node`传递一些参数：

◼ 如果我们这样来使用程序，就意味着我们需要在程序中获取到传递的参数：

- 获取参数其实是在`**process**`**的内置对象中**的；
- 如果我们直接打印这个内置对象，它里面包含特别的信息：

◼ 现在，我们先找到其中的`argv`属性：

- 我们发现它是一个**数组**，里面包含了我们需要的参数；

```javascript
// 给程序输入内容
const arg1 = process.argv[2]
const arg2 = process.argv[3]
console.log(process.argv)
```

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202306100056689.png)

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202306100056696.png)



## 8. 为什么叫argv呢？

◼ 你可能有个疑问，为什么叫`argv`呢？

◼ 在`C/C++`程序中的`main`函数中，实际上可以获取到两个参数：

- `argc`：argument counter的缩写，传递参数的个数；
- `argv`：argument vector（向量、矢量）的缩写，传入的具体参数。

- - `vector`翻译过来是矢量的意思，在程序中表示的是一种数据结构。
  - 在`C++、Java`中都有这种数据结构，是一种数组结构；
  - 在`JavaScript`中也是一个数组，里面存储一些参数信息；

◼ 我们可以在代码中，将这些参数信息遍历出来，使用：

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202306100056708.png)



## 9. Node的输出

◼ `console.log`

- 最常用的输入内容的方式：console.log

◼ `console.clear`

- 清空控制台：console.clear

◼ `console.trace`

- 打印函数的调用栈：console.trace

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202306100056676.png)



## 10. 特殊的全局对象

◼ 为什么我称之为特殊的全局对象呢？

- 这些全局对象实际上是模块中的变量，只是每个模块都有，看来像是全局变量；
- 在命令行交互中是不可以使用的；
- 包括：__dirname、__filename、exports、module、require()

◼ `__dirname`：获取当前文件所在的**路径**：

◼ `__filename`：获取当前文件所在的**路径和文件名称**：

```javascript
console.log(__dirname)
console.log(__filename)
```

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202306100056657.png)

## 11. 常见的全局对象

◼ `process`对象：`process`提供了`**Node**`**进程中相关的信息**：

- 比如`Node`的运行环境、参数信息等；
-  后面在项目中，我也会讲解，如何将一些环境变量读取到 `process `的 `env` 中；

◼ `console`对象：提供了简单的调试控制台，在前面讲解输入内容时已经学习过了。

◼ **定时器函数**：在Node中使用定时器有好几种方式：

- `setTimeout(callback, delay[, ...args])`：`callback`在`delay`毫秒后执行一次；
- `setInterval(callback, delay[, ...args])`：`callback`每`delay`毫秒重复执行一次；
- `setImmediate(callback[, ...args])`：`callbackI / O`事件后的回调的“立即”执行；

- - 这里先不展开讨论它和`setTimeout(callback, 0)`之间的区别；
  - 因为它涉及到事件循环的阶段问题，我会在后续详细讲解事件循环相关的知识；

- `process.nextTick(callback[, ...args])`：添加到下一次`tick`队列中；

- - 具体的讲解，也放到事件循环中说明

```javascript
setImmediate(() => {   // 最后执行
  console.log("setImmediate")
})

setTimeout(() => {  
    console.log("setTimeout");
}, 0);

// 额外执行函数
process.nextTick(() => {   // 最先执行
  console.log("nextTick")
})

// nextTick
// setTimeout
// setImmediate
```



## 12. global对象 

◼ `global`是一个全局对象, 事实上前端我们提到的process,console,setTimeout等都有被放到`global`中：

- 我们之前讲过：在新的标准中还有一个`globalThis`，也是指向全局对象的；
- 类似于浏览器中的`window`；

```javascript
console.log(global === globalThis)
// true
```

## 13. global和window的区别

◼ 在浏览器中，全局变量都是在window上的，比如有document、setInterval、setTimeout、alert、console等等

◼ 在Node中，我们也有一个global属性，并且看起来它里面有很多其他对象。

◼ 但是在浏览器中执行的JavaScript代码，如果我们在顶级范围内通过var定义的一个属性，默认会被添加到window对象上：

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202306100056693.png)

◼ 但是**在node中，我们通过var定义一个变量，它只是在当前模块中有一个变量，不会放到全局中：**

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202306100056700.png)