## 1. 认识应用状态管理

将Vuex称为状态管理而不是数据管理是因为它更准确地描述了Vuex的功能和职责。它管理着整个应用程序的状态，包括数据、状态变化和行为，而不仅仅是单纯的数据管理。

### 1.1. 什么是状态管理

◼ 在开发中，我们会的应用程序需要处理各种各样的数据，这些数据需要保存在我们应用程序中的某一个位置，对于这些数据的管理我们就称之为是 状态管理。

◼ 在前面我们是如何管理自己的状态呢？

*  在Vue开发中，我们使用组件化的开发方式；
*  而在组件中我们定义data或者在setup中返回使用的数据，**这些数据我们称之为**`**state**`**；**
*  在模块template中我们可以使用这些数据，模块最终会被**渲染成**`**DOM**`**，我们称之为**`**View**`；
*  在模块中我们会产生一些行为事件，处理这些行为事件时，有可能会修改`state`，这些**行为事件我们称之为**`**actions**`**；**

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913337.png)

### 1.2. 复杂的状态管理

◼ JavaScript开发的应用程序，已经变得越来越复杂了：

*  JavaScript需要管理的状态越来越多，越来越复杂；
*  这些状态包括服务器返回的数据、缓存数据、用户操作产生的数据等等；
*  也包括一些UI的状态，比如某些元素是否被选中，是否显示加载动效，当前分页；

◼ 当我们的应用遇到多个组件共享状态时，单向数据流的简洁性很容易被破坏：

*  多个视图依赖于同一状态；
*  来自不同视图的行为需要变更同一状态 ;

◼ 我们是否可以通过组件数据的传递来完成呢？

*  对于一些简单的状态，确实可以通过props的传递或者Provide的方式来共享状态；
*  但是对于复杂的状态管理来说，显然单纯通过传递和共享的方式是不足以解决问题的，比如兄弟组件如何共享数据呢？

### 1.3. Vuex的状态管理

◼ 管理不断变化的state本身是非常困难的：

*  状态之间相互会存在依赖，一个状态的变化会引起另一个状态的变化，View页面也有可能会引起状态的变化；
*  当应用程序复杂时，state在什么时候，因为什么原因而发生了变化，发生了怎么样的变化，会变得非常难以控制和追踪；

◼ 因此，我们是否可以考虑将组件的内部状态抽离出来，以一个全局单例的方式来管理呢？

*  在这种模式下，我们的组件树构成了一个巨大的 “试图View”；
*  不管在树的哪个位置，任何组件都能获取状态或者触发行为；
*  通过定义和隔离状态管理中的各个概念，并通过强制性的规则来维护视图和状态间的独立性，我们的代码边会变得更加结构化和易于维护、跟踪；

◼ 这就是Vuex背后的基本思想，它借鉴了Flux、Redux、Elm（纯函数语言，redux有借鉴它的思想）；

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913254.png)![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913302.png)

## 2. Vuex的基本使用

### 2.1. 创建Store

◼ 每一个`Vuex`应用的核心就是`store`（仓库）：

*  `store`本质上是一个容器，它包含着你的应用中大部分的状态（state）；

◼ `Vuex`和单纯的全局对象有什么区别呢？

◼ 第一：Vuex的状态存储是**响应式**的

*  当Vue组件从store中读取状态的时候，**若store中的状态发生变化，那么相应的组件也会被更新；**

◼ 第二：你**不能直接改变store中的状态**

*  **改变store中的状态的唯一途径就显示提交 (commit) mutation；**
*  这样使得我们可以方便的跟踪每一个状态的变化，从而让我们能够通过一些工具帮助我们更好的管理应用的状态；

◼ 使用步骤：

*  创建`Store`对象；
*  在`app`中通过插件安装；

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913247.png)

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913366.png)

### 2.2. 单一状态树

◼ Vuex 使用单一状态树：

*  用一个对象就包含了全部的应用层级的状态；
*  采用的是SSOT，Single Source of Truth，也可以翻译成单一数据源；

◼ 这也意味着，每个应用将仅仅包含一个 store 实例；

*  单状态树和模块化并不冲突，后面我们会讲到module的概念；

◼ 单一状态树的优势：

*  如果你的状态信息是保存到多个Store对象中的，那么之后的管理和维护等等都会变得特别困难；
*  所以Vuex也使用了单一状态树来管理应用层级的全部状态；
*  单一状态树能够让我们用最直接的方式找到某个状态的片段；
*  而且在之后的维护和调试过程中，也可以非常方便的管理和维护；

## 3. 核心概念State

### 3.1. State基本使用

* 在模板中使用；
* 在options api中使用，比如computed；
* 在setup中使用；

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913263.png)

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913016.png)

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913088.png)



```
store`里面的`state
```

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913062.png)

### 3.2. State--MapState映射

这段代码为要在setup中使用mapState封装的简便函数`useState`

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913149.png)



template

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913107.png)



optionsAPI中使用状态

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913068.png)



compositionAPI中使用状态

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913348.png)

上述代码 1, 2 两点方法相同, 2只是调用封装的函数罢了





## 4. 核心概念Getters

◼ 某些属性我们可能需要经过变化后来使用，这个时候可以使用getters：

### 4.1. Getters的基本使用

◼ getters中的函数可以接收第二个参数：

◼ getters中的函数本身，可以返回一个函数，那么在使用的地方相当于可以调用这个函数：

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913487.png)

注意`getFriendByid`的妙用, 返回一个可传参数的函数



![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913497.png)



### 4.2. mapGetters映射

template

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913516.png)



optionsAPI

这里我们也可以使用mapGetters的辅助函数

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913558.png)



compositionAPI

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913660.png)

## 5. 核心概念Mutations

更改 `Vuex` 中 `store` 中状态的唯一方法是提交`mutation`

### 5.1. mutations基本使用

不能执行异步操作 , 异步操作请去Actions

◼ 很多时候我们在提交mutation的时候，会携带一些数据，这个时候我们可以使用参数

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913811.png)

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913889.png)

### 5.2. mapMutations映射

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913911.png)



借助于辅助函数`mapMutations`，帮助我们快速映射到对应的方法中：

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913903.png)

显而易见`optionsAPI`简单许多

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913222.png)

### 5.3. mutations重要原则

◼ 一条重要的原则就是要记住 mutation 必须是同步函数

*  这是因为devtool工具会记录mutation的日记；
*  每一条mutation被记录，devtools都需要捕捉到前一状态和后一状态的快照；
*  但是在mutation中执行异步操作，就无法追踪到数据的变化；

◼ 所以Vuex的重要原则中要求 mutation必须是同步函数；

* 但是如果我们希望在Vuex中发送网络请求的话需要如何操作呢？

**总结 : 如果在mutation中使用异步操作, 则难以追踪到数据的变化**

## 6. 核心概念Actions

### 6.1. Actions基本使用

◼ `Action`类似于`mutation`，不同在于：

*  `Action`**提交的是**`**mutation**`，而不是直接变更状态；
*  `Action`可以**包含任意异步操作；**

◼ 这里有一个非常重要的参数`context`：

*  context是一个和`store`实例均有相同方法和属性的`context`对象；
*  所以我们可以从其中获取到`commit`方法来提交一个`mutation`，或者通过 `context.state` 和 `context.getters` 来获取 `state `和`getters`；

◼ 但是为什么它不是store对象呢？这个等到我们讲Modules时再具体来说；

目的 : 提交mutation

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913151.png)



◼ 如何使用action呢？进行action的分发：

* 分发使用的是 `store` 上的`dispatch`函数
* 同样的，它也可以携带参数

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913254.png)

### 6.2. mapActions映射

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913257.png)

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913366.png)

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913474.png)

当觉得麻烦的时候使用默认做法即可



### 6.3. 发送网络请求

◼ Action 通常是异步的，那么如何知道 action 什么时候结束呢？

*  我们可以通过让action返回Promise，在Promise的then中来处理完成后的操作；

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913547.png)![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913639.png)

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913751.png)

结合store里的代码

## 7. 核心概念Modules

◼ 什么是Module？

*  由于使用单一状态树，应用的所有状态会集中到一个比较大的对象，当应用变得非常复杂时，store 对象就有可能变得相当臃肿；
*  为了解决以上问题，Vuex 允许我们将 store 分割成模块（module）；
*  **每个模块拥有自己的 state、mutation、action、getter、甚至是嵌套子模块**

### 7.1. modules的基本使用

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913800.png)

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913672.png)![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913823.png)





◼ 对于模块内部的 mutation 和 getter，接收的第一个参数是模块的局部状态对象：

home.js

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913066.png)



couter.js

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913097.png)



### 7.2. modules默认模块化

注意看注释

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913226.png)



### 7.3. module的命名空间

◼ 默认情况下，模块内部的action和mutation仍然是注册在全局的命名空间中的：

这样可能会导致命名冲突

*  这样使得多个模块能够对同一个 action 或 mutation 作出响应；
*  Getter 同样也默认注册在全局命名空间；

◼ 如果我们希望模块具有更高的封装度和复用性，可以添加 `namespaced: true `的方式使其成为带命名空间的模块：

*  当模块被注册后，它的所有 getter、action 及 mutation 都会自动根据模块注册的路径调整命名；

couter.js

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913194.png)

加了命名空间之后, 注意注释

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260913234.png)