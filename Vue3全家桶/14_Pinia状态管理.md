## 1. Pinia和Vuex的对比

### 1.1. 什么是Pinia呢？

◼ Pinia（发音为/piːnjʌ/，如英语中的“peenya”）是最接近piña（西班牙语中的菠萝）的词；

*  Pinia开始于大概2019年，最初是作为一个实验为Vue重新设计状态管理，让它用起来像组合式API（Composition API）。
*  从那时到现在，最初的设计原则依然是相同的，并且目前**同时兼容Vue2、Vue3**，也并不要求你使用Composition API；
*  Pinia本质上依然是一个**状态管理的库，用于跨组件、页面进行状态共享**（这点和Vuex、Redux一样）；

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260915123.png)

### 1.2. Pinia和Vuex的区别

◼ 那么我们不是已经有Vuex了吗？为什么还要用Pinia呢？

*  Pinia 最初是为了探索 Vuex 的下一次迭代会是什么样子，结合了 Vuex 5 核心团队讨论中的许多想法；
*  最终，团队意识到Pinia已经实现了Vuex5中大部分内容，所以最终决定**用Pinia来替代Vuex**；
*  与 Vuex 相比，Pinia 提供了一个**更简单的 API，具有更少的仪式，提供了 Composition-API 风格的 API；**
*  最重要的是，在**与 TypeScript 一起使用时具有可靠的类型推断支持；**

◼ 和Vuex相比，Pinia有很多的**优势**：

*  比如`mutations` **不再存在：**

* 他们经常被认为是 非常 冗长；
* 他们最初带来了 devtools 集成，但这不再是问题；

*  **更友好的**`TypeScript`**支持，**Vuex之前对TS的支持很不友好；
*  **不再有**`modules`**的嵌套结构：**

* 你可以灵活使用每一个store，它们是通过扁平化的方式来相互使用的；

*  也不再有命名空间的概念，不需要记住它们的复杂关系；

## 2. 创建Pinia的Store

### 2.1. 认识Store

◼ 什么是Store？

*  一个 Store （如 Pinia）是一个实体，它会持有为绑定到你组件树的状态和业务逻辑，也就是保存了全局的状态；
*  它有点像始终存在，并且每个人都可以读取和写入的组件；
*  你可以在你的应用程序中定义任意数量的Store来管理你的状态；

◼ Store有三个核心概念：

*  state、getters、actions；
*  等同于组件的data、computed、methods；
*  一旦 store 被实例化，你就可以直接在 store 上访问 state、getters 和 actions 中定义的任何属性；



◼ 使用Pinia之前，我们需要先对其进行安装

```
npm install pinia
```

◼ 创建一个`pinia`并且将其传递给应用程序 

src目录

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260915139.png)



index.js

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260915129.png)



main.js

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260915134.png)

### 2.2. 定义一个Store

◼ 定义一个Store：

*  我们需要知道 Store 是使用 `defineStore() `定义的，
*  并且它需要一个**唯一名称**，作为**第一个参数**传递；

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260915107.png)

◼ 这个 counter，也称为 `id`，是必要的，Pinia 使用它来将 store 连接到 devtools。

◼ 返回的函数统一使用`useXXX`作为命名方案，这是约定的规范；



### 2.3. 使用定义的Store

◼ Store在它被使用之前是不会创建的，我们可以通过**调用use函数来使用Store：**

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260915185.png)



◼ 注意`Store`获取到后不能被解构，那么会失去响应式：

*  为了从 Store 中提取属性同时保持其响应式，您需要使用`storeToRefs()`或者 `toRefs()`。

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260915444.png)

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260915486.png)

## 3. Pinia核心概念State

### 3.1. 认识和定义State

◼ state 是 store 的核心部分，因为store是用来帮助我们管理状态的。

* 在 Pinia 中，状态被定义为返回初始状态的函数；

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260915506.png)

箭头函数返回一个对象要用`()`包裹



### 3.2. 操作State

◼ **读取和写入 state：**

* 默认情况下，您可以通过 store 实例访问状态来直接读取和写入状态；

◼ **重置 State：**

* 你可以通过调用 store 上的 $reset() 方法将状态 重置 到其初始值；

◼ **改变State：**

*  除了直接用 store.counter++ 修改 store，你还可以调用 $patch 方法；
*  它允许您使用部分“state”对象同时应用多个更改；

◼ **替换State：**

* 可以通过将其 $state 属性设置为新对象来替换 Store 的整个状态：

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260915599.png)

注意看注释

## 4. Pinia核心概念Getters

### 4.1. 认识和定义Getters

◼ Getters相当于Store的计算属性：

*  它们可以用 **defineStore() 中的 getters 属性定义**；
*  getters中可以**定义接受一个state作为参数的函数**

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260915561.png)



### 4.2. 访问Getters

◼ 访问当前`store`的`Getters`：
◼ `Getters`中访问自己`store`中的其他`Getters`：

* 我们可以通过`this`来访问到当前store实例的所有其他属性;

◼ 访问其他store的Getters：

◼ Getters也可以返回一个函数，这样就可以接受参数：

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260915588.png)

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260915748.png)

## 5. Pinia核心概念Actions

### 5.1. 认识和定义Actions

◼ Actions 相当于组件中的 methods。

* 可以使用 `defineStore()` 中的 `actions` 属性定义，并且它们非常适合**定义业务逻辑**；

◼ 和getters一样，在action中可以通过`this`访问整个`store`实例的所有操作；

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260915784.png)

### 5.2. Actions执行异步操作

◼ `Actions`中是支持异步操作的，并且我们可以编写异步函数，**在函数中使用await**

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260915875.png)



counter的actions

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260915029.png)

home的actions

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260915005.png)