## 1. CompositionAPI

### 1.1. Options API的弊端

◼ 在Vue2中，我们编写组件的方式是Options API：

*  Options API的一大特点就是在**对应的属性**中**编写对应的功能模块**；
*  比如data定义数据、methods中定义方法、computed中定义计算属性、watch中监听属性改变，也包括生命周期钩子；

◼ 但是这种代码有一个很大的**弊端：**

*  当我们实现某一个功能时，这个功能对应的代码逻辑会被拆分到各个属性中；
*  当我们组件变得更大、更复杂时，逻辑关注点的列表就会增长，那么**同一个功能的逻辑就会被拆分的很分散**；
*  尤其对于那些一开始没有编写这些组件的人来说，这个组件的代码是**难以阅读和理解的**（阅读组件的其他人）；

◼ 下面我们来看一个非常大的组件，其中的逻辑功能按照颜色进行了划分：

*  这种碎片化的代码使用**理解和维护这个复杂的组件变得异常困难，并且隐藏了潜在的逻辑问题**；
*  并且当我们处理单个逻辑关注点时，需要不断的跳到相应的代码块中；

◼ 如果我们能将同一个逻辑关注点相关的代码收集在一起会更好。

◼ 这就是Composition API想要做的事情，以及可以帮助我们完成的事情。

◼ 也有人把Vue CompositionAPI简称为`**VCA**`。



### 1.2. 认识Composition API

◼ 那么既然知道Composition API想要帮助我们做什么事情，接下来看一下到底是怎么做呢？

*  为了开始使用Composition API，我们需要有一个可以实际使用它（编写代码）的地方；
*  在Vue组件中，这个位置就是 `**setup**` **函数；**

◼ `setup`其实就是组件的另外一个选项：

*  只不过这个选项强大到我们可以用它来替代之前所编写的大部分其他选项；
*  比如`methods`、`computed`、`watch`、`data`、`生命周期`等等；

◼ 接下来我们一起学习这个函数的使用：

*  函数的参数
*  函数的返回值

## 2. Setup函数的基本使用

### 2.1. setup函数的参数

◼ 我们先来研究一个`setup`函数的参数，它主要有**两个参数**：

*  第一个参数：`props`
*  第二个参数：`context`

◼ `**props**`非常好理解，它其实就是父组件传递过来的属性会被放到`props`对象中，我们在`setup`中如果需要使用，那么就可以直接

通过`props`参数获取：

*  对于定义`props`的类型，我们还是和之前的规则是一样的，在`props`选项中定义；
*  并且在`template`中依然是可以正常去使用`props`中的属性，比如`message`；
*  如果我们在`setup`函数中想要使用`props`，那么**不可以**通过 `this` 去获取（后面我会讲到为什么）；
*  因为`props`有直接作为参数传递到`setup`函数中，所以我们可以直接通过参数来使用即可；

◼ 另外一个参数是`**context**`，我们也称之为是一个`SetupContext`，它里面包含三个属性：

*  `attrs`：所有的`非prop`的`attribute`；
*  `slots`：父组件传递过来的插槽（这个在以渲染函数返回时会有作用，后面会讲到）；
*  `emit`：当我们组件内部需要发出事件时会用到`emit`（因为我们不能访问`this`，所以不可以通过`this.$emit`发出事件）

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1686474395529-385159c4-285c-4817-96b6-25c24b4ad8fd.png)



### 2.2. setup函数的返回值

◼ `setup`既然是一个函数，那么它也可以有返回值，它的返回值用来做什么呢？

*  `setup`的返回值可以在模板`template`中被使用；
*  也就是说我们可以通过`setup`的返回值来替代`data`选项；

◼ 甚至是我们可以返回一个执行函数来代替在`methods`中定义的方法

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1686196260197-e4391ef9-805c-4c79-9d41-1ec977309509.png)

◼ 但是，如果我们将 `counter` 在 `increment` 或者 `decrement`进行操作时，是否可以实现界面的响应式呢？

*  答案是不可以；
*  这是因为对于一个定义的变量来说，默认情况下，`Vue`并不会跟踪它的变化，来引起界面的响应式操作；

## 3. Setup中数据的响应式

### 3.1. Reactive API

◼ 如果想为在`setup`中定义的数据提供响应式的特性，那么我们可以使用`reactive`的函数：

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1686196548287-9761f76d-fc3f-4c47-8587-adebd71714e8.png)

◼ 那么这是什么原因呢？为什么就可以变成响应式的呢？

*  这是因为当我们使用`reactive`函数处理我们的数据之后，**数据再次被使用时就会进行依赖收集；**
*  当数据发生改变时，所有收集到的依赖都是进行对应的响应式操作（比如更新界面）；
*  事实上，我们编写的`data`选项，也是在内部交给了`reactive`函数将其编程响应式对象的；

### 3.2. Ref API

◼ reactive API对传入的类型是有限制的，它要求我们必须传入的是一个**对象**或者**数组**类型：

* 如果我们传入一个基本数据类型（String、Number、Boolean）会报一个警告；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1686196590146-47d75083-b058-422b-b2cc-b30749977208.png)

◼ 这个时候`Vue3`给我们提供了另外一个`API`：`ref API`

*  `ref` 会返回一个可变的响应式对象，该对象作为一个 响应式的引用 维护着它内部的值，这就是`ref`名称的来源；
*  它内部的值是在`ref`的 `value` 属性中被维护的；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1686196625975-153de01d-9dad-4be5-84cb-e80f83db0e92.png)

◼ 这里有两个注意事项：

* 在模板中引入`ref`的值时，`Vue`会自动帮助我们进行解包操作，所以我们并不需要在模板中通过 `ref.value` 的方式来使用；
* 但是在 `setup`函数内部，它依然是一个 `ref`引用， 所以对其进行操作时，我们依然需要使用 `ref.value`的方式；

### 3.3. Ref自动解包

◼ 模板中的解包是浅层的解包，如果我们的代码是下面的方式：

◼ 如果我们将`ref`放到一个`reactive`的属性当中，那么在模板中使用时，它会自动解包：

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1686474127011-2cb1602b-1f83-4606-aea3-f78f06a0fe75.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1686474138656-4624d7d6-8acb-4b52-86d8-80de32609f7d.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1686474300781-0c3945a6-b189-403b-a9fa-284e506581e0.png)

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282038723.png)

## 4. Readonly

### 4.1. 认识 readonly

◼ 我们通过`reactive`或者`ref`可以获取到一个响应式的对象，但是某些情况下，我们传入给其他地方（组件）的这个响应式对象**希望在另外一个地方（组件）被使用，但是不能被修改**，这个时候如何防止这种情况的出现呢？

单项数据流

*  Vue3为我们提供了`readonly`的方法；
*  readonly会返回原始对象的**只读代理**（也就是它依然是一个Proxy，这是一个proxy的set方法被劫持，并且不能对其进行修改）；

◼ 在开发中常见的`readonly`方法会传入三个类型的参数：

*  类型一：普通对象；
*  类型二：reactive返回的对象；
*  类型三：ref的对象；

### 4.2. readonly的使用

◼ 在readonly的使用过程中，有如下规则：

*  `readonly`返回的对象都是**不允许修改**的；
*  但是经过`readonly`处理的原来的对象是允许被修改的；

* * ✓ 比如 const info = readonly(obj)，info对象是不允许被修改的；
  * ✓ 当obj被修改时，readonly返回的info对象也会被修改；
  * ✓ 但是我们不能去修改readonly返回的对象info；

◼ 其实本质上就是`readonly`返回的对象的`setter`方法被劫持了而已；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1686473208694-210d9b1f-b8f5-4fbe-b0a7-54c0274e1dcd.png)



### 4.3. readonly的应用

◼ 那么这个readonly有什么用呢？

* 在我们传递给其他组件数据时，往往希望其他组件使用我们传递的内容，但是不允许它们修改时，就可以使用readonly了；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1686473302217-58ec781f-5442-4efe-ad30-7d178874eb53.png)

## 5. Reactive知识点补充

### 5.1. Reactive判断的API

◼ `isProxy`

* 检查对象是否是由 reactive 或 readonly创建的 proxy。

◼ `isReactive`

* 检查对象是否是由 reactive创建的响应式代理：
* 如果该代理是 readonly 建的，但包裹了由 reactive 创建的另一个代理，它也会返回 true；

◼ `isReadonly`

* 检查对象是否是由 readonly 创建的只读代理。

◼ `toRaw`

* 返回 reactive 或 readonly 代理的原始对象（不建议保留对原始对象的持久引用。请谨慎使用）。

◼ `shallowReactive`

* 创建一个响应式代理，它跟踪其自身 property 的响应性，但不执行嵌套对象的深层响应式转换 (深层还是原生对象)。

◼ `shallowReadonly`

* 创建一个 proxy，使其自身的 property 为只读，但不执行嵌套对象的深度只读转换（深层还是可读、可写的）。

### 5.2. toRefs

◼ 如果我们使用ES6的解构语法，对reactive返回的对象进行解构获取值，那么之后无论是修改结构后的变量，还是修改reactive返回的state对象，数据都不再是响应式的：

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1686473436168-4baaaf34-0637-4c58-86c2-2f706f550bfc.png)

◼ 那么有没有办法让我们解构出来的属性是响应式的呢？

* Vue为我们提供了一个toRefs的函数，可以将reactive返回的对象中的属性都转成ref；
* 那么我们再次进行结构出来的 name 和 age 本身都是 ref的；

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282038645.png)

◼ 这种做法相当于已经在`state.name`和`ref.value`之间建立了 链接，任何一个修改都会引起另外一个变化；

记得![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282038658.png)

### 5.3. toRef 

◼ 如果我们只希望转换一个`reactive`对象中的属性为ref, 那么可以使用toRef的方法：

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282038743.png)



## 6. Ref知识点补充

◼  `unref` 如果我们想要获取一个ref引用中的value，那么也可以通过unref方法：

* 如果参数是一个 ref，则返回内部值，否则返回参数本身；
* 这是 val = isRef(val) ? val.value : val 的语法糖函数；

◼ `isRef`

* 判断值是否是一个ref对象。

◼ `shallowRef`

* 创建一个浅层的ref对象；

◼ `triggerRef`

* 手动触发和 shallowRef 相关联的副作用：

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282038716.png)

## 7. setup中禁用this

官方文档的解释

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282038723.png)	