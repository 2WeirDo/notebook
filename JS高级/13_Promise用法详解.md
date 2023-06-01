## 1. 异步任务的处理

◼ 从一个实际的例子来作为切入点：

- 我们调用一个函数，这个函数中发送网络请求（我们可以用定时器来模拟）；
- 如果发送网络请求成功了，那么告知调用者发送成功，并且将相关数据返回过去；
- 如果发送网络请求失败了，那么告知调用者发送失败，并且告知错误信息；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682096934606-87ebdc0b-c914-4964-80b7-92aee9b3f550.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682096940084-0467bbdf-bb10-4d0a-8a12-6e2f9d5efc41.png)



## 2. Promise的含义

Promise 是**异步编程**的一种解决方案，比传统的解决方案——回调函数和事件——更合理和更强大。它由社区最早提出和实现，ES6 将其写进了语言标准，统一了用法，原生提供了Promise对象。

所谓Promise，简单说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。从语法上说，`Promise` 是一个对象，从它可以获取异步操作的消息。`Promise` 提供统一的 `API`，各种异步操作都可以用同样的方法进行处理。

◼ 在上面的解决方案中，我们确确实实可以解决请求函数得到结果之后，获取到对应的回调，但是它存在两个主要的问题：

-  第一，我们需要自己来设计回调函数、回调函数的名称、回调函数的使用等；
-  第二，对于不同的人、不同的框架设计出来的方案是不同的，那么我们必须耐心去看别人的源码或者文档，以便可以理解它



◼ 我们来看一下`Promise`的`API`是怎么样的：

- `Promise`是一个类，它的英语意思就是“承诺”，表示其他手段无法改变。
- 当我们需要的时候，给予调用者一个承诺：待会儿我会给你回调数据时，就可以创建一个`Promise`的对象；
- 在通过`new`创建`Promise`对象时，我们需要传入一个回调函数，我们称之为`executor`

- - 这个回调函数会被**立即执行**，并且给传入另外两个回调函数`resolve`、`reject`；
  - 当我们调用`resolve`回调函数时，会执行`Promise`对象的`then`方法传入的回调函数；
  - 当我们调用`reject`回调函数时，会执行`Promise`对象的`catch`方法传入的回调函数；



![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682097184452-ccbad5c8-bb8a-4e6d-a65e-33cb5213f623.png)

上面`Promise`使用过程，我们可以将它划分成三个状态：

- 待定（`pending`）: 初始状态，既没有被兑现，也没有被拒绝；

- -  当执行`executor`中的代码时，处于该状态；

- 已兑现（`fulfilled`）: 意味着操作成功完成；

- -  执行了`resolve`时，处于该状态，`Promise`已经被兑现；

- 已拒绝（`rejected`）: 意味着操作失败；

- - 执行了`reject`时，处于该状态，`Promise`已经被拒绝；







## 3. Executor

◼ Executor 是在创建`Promise`时需要传入的一个回调函数，这个回调函数会被**立即执行**，并且传入两个参数：

**Promise 新建后就会立即执行。**

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682178726677-1b65fdd0-129e-442c-9e41-88bc0525d580.png)

上面代码中，Promise 新建后立即执行，所以首先输出的是Promise。然后，then方法指定的回调函数，将在当前脚本所有同步任务执行完才会执行，所以resolved最后输出。





**注意，调用**`resolve`或`reject`并不会终结 `Promise` 的参数函数的执行。

> 因为 resolve -- then 是一个微任务

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682179139397-5740b94e-7290-4259-8f20-0a044b235d2f.png)

上面代码中，调用resolve(1)以后，后面的console.log(2)还是会执行，并且会首先打印出来。这是因为立即 resolved 的 Promise 是在本轮事件循环的末尾执行，总是晚于本轮循环的同步任务。



一般来说，调用`resolve`或`reject`以后，`Promise` 的使命就完成了，后继操作应该放到`then`方法里面，而不应该直接写在`resolve`或`reject`的后面。所以，最好在它们前面加上`return`语句，这样就不会有意外。

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682179272670-440dd029-0545-477a-b4b5-05f1d3d68021.png)



◼ 通常我们会在`Executor`中确定我们的`Promise`状态：

- 通过`resolve`，可以兑现（`fulfilled`）`Promise`的状态，我们也可以称之为已决议（resolved）；
- 通过`reject`，可以拒绝（`reject`）`Promise`的状态；



◼ 这里需要注意：一旦状态被确定下来，`Promise`的状态会被 锁死，该`Promise`的状态是不可更改的

- 在我们调用`resolve`的时候，如果`resolve`传入的值本身不是一个`Promise`，那么会将该`Promise`的状态变成兑现（`fulfilled`）；
- 在之后我们去调用`reject`时，已经不会有任何的响应了（并不是这行代码不会执行,  而是无法改变`Promise`状态）；



## 4. resolve不同值的区别

◼情况一：如果resolve传入一个普通的值或者对象，那么这个值会作为then回调的参数；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682125960271-ef4d3f93-1efe-4dfb-9537-accee2a0f558.png)



◼ 情况二：如果resolve中传入的是另外一个Promise，那么这个新Promise会决定原Promise的状态：

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682125971698-0d71bea0-aa92-42ad-9ad6-afd56d1ef1f0.png)



◼ 情况三：如果resolve中传入的是一个对象，并且这个对象有实现then方法，那么会执行该then方法，并且根据then方法的结果来决定Promise的状态：

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682125983995-7e80097b-fef7-405d-b369-f5265604e630.png)



## 5. then方法

### 5.1. then方法 – 接受两个参数

◼ `then`方法是`Promise`对象上的一个方法（实例方法）：

- 它其实是放在`Promise`的**原型**上的 `Promise.prototype.then`

◼ `then`方法接受两个参数：

- `fulfilled`的回调函数：当状态变成`fulfilled`时会回调的函数；
-  `reject`的回调函数：当状态变成`reject`时会回调的函数；



![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682126038167-22979e05-a1d0-4b79-a2d6-6689dde93e4b.png)



**一般来说，不要在`then()`方法里面定义 Reject  状态的回调函数（即`then`的第二个参数），总是使用`catch`方法。**

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682179526556-2629b7e9-e481-44b2-aa97-4339c1d7990a.png)

上面代码中，第二种写法要好于第一种写法，理由是**第二种写法可以捕获前面**then`方法执行中的错误，也更接近同步的写法（try/catch）。因此，建议总是使用catch()方法，而不使用then()方法的第二个参数。



### 5.2. then方法 – 多次调用

◼ 一个`Promise`的`then`方法是可以被多次调用的：

- 每次调用我们都可以传入对应的`fulfilled`回调；
-  当`Promise`的状态变成`fulfilled`的时候，这些回调函数都会被执行；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682178202473-00fe05dc-3b7a-43d5-9c19-a882eeab38cf.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682178210653-6c87f08c-c7d3-4e19-8bad-463307b07cde.png)





### 5.3. then方法 – 返回值

◼ `then`方法本身是有返回值的，它的返回值是一个`Promise`，所以我们可以进行如下的链式调用：

- 但是`then`方法返回的`Promise`到底处于什么样的状态呢？

◼ `Promise`有三种状态，那么这个`Promise`处于什么状态呢？

- 当`then`方法中的回调函数本身在执行的时候，那么它处于`pending`状态
- 当`then`方法中的回调函数返回一个结果时，那么它处于`fulfilled`状态，并且会将结果(`return`)作为`resolve`的参数；

- - 情况一：返回一个普通的值；
  - 情况二：返回一个`Promise`；
  - 情况三：返回一个`thenable`值；



 当`then`方法抛出一个异常时，那么它处于`reject`状态；



## 6. catch方法

### 6.1. catch方法 – 多次调用

◼ `catch`方法也是`Promise`对象上的一个方法（实例方法）：

- 它也是放在`Promise`的**原型**上的 `Promise.prototype.catch`

◼ 一个`Promise`的`catch`方法是可以被多次调用的：

- 每次调用我们都可以传入对应的`reject`回调；
- 当`Promise`的状态变成`reject`的时候，这些回调函数都会被执行；



### 6.2. catch方法的执行时机

◼ 事实上`catch`方法也是会返回一个`Promise`对象的，所以`catch`方法后面我们可以继续调用`then`方法或者`catch`方法：

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682178099689-179dd83b-b48e-4250-b74d-ad0e2e87d899.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682178106736-4187d2c9-bf71-4dbb-87f3-1491f5477378.png)



- 遇到抛出错误后会往 `catch` 去找

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682176774818-98b9378d-92fa-4f60-8e52-2849963c33b9.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682176781698-4a843f9c-ce53-40c5-ab75-dfbcb9095610.png)



Promise 对象的错误具有“冒泡”性质，会一直向后传递，直到被捕获为止。也就是说，错误总是会被下一个catch语句捕获。

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682179467202-1036bda7-e0c1-4207-86e0-c12ee7c5ef78.png)

上面代码中，一共有三个 Promise 对象：一个由getJSON()产生，两个由then()产生。它们之中任何一个抛出的错误，都会被最后一个catch()捕获。



## 7. finally方法

◼`finally`是在ES9（ES2018）中新增的一个特性：表示无论`Promise`对象无论变成`fulfilled`还是`rejected`状态，最终都会被执行的代码。

◼ `finally`方法是不接收参数的，因为无论前面是`fulfilled`状态，还是`rejected`状态，它都会执行.

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682126366055-362c31e6-e027-4416-b967-8b0581a3bb39.png)





## 8. Promise类方法

### 8.1. Promise.resolve()

**有时需要将现有对象转为** `Promise` **对象，`Promise.resolve()`方法就起到这个作用。**

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682180139284-370d058e-d127-4a01-be5b-9defbcded4e6.png)





◼ 有时候我们已经有一个现成的内容了，希望将其转成`Promise`来使用

这个时候我们可以使用 `Promise.resolve` 方法来完成。

 `Promise.resolve`的用法相当于`new Promise`，并且执行`resolve`操作

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682126422048-795b2fe0-7b91-4c91-8147-febeb0ec8598.png)

◼ `resolve`参数的形态：

 情况一：参数是一个普通的值或者对象

 情况二：参数本身是`Promise`

 情况三：参数是一个`thenable`



需要注意的是，立即resolve()的 Promise 对象，是在本轮“事件循环”（event loop）的结束时执行，而不是在下一轮“事件循环”的开始时。

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682180174245-055853f1-6e47-46ac-96db-7b1df1ae2162.png)

上面代码中，setTimeout(fn, 0)在下一轮“事件循环”开始时执行，Promise.resolve()在本轮“事件循环”结束时执行，console.log('one')则是立即执行，因此最先输出。

涉及宏任务微任务 ---- 微任务为空时再执行宏任务

### 8.2. Promise.reject()

◼ `reject`方法类似于`resolve`方法，只是会将`Promise`对象的状态设置为`reject`状态。

◼ `Promise.reject`的用法相当于`new Promise`，只是会调用`reject`：

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682126474888-402fc5d0-8fe9-487d-afa9-a798e3ab45cd.png)

◼ `Promise.reject`传入的参数无论是什么形态，都会直接作为`reject`状态的参数传递到`catch`的。



### 8.3. Promise.all()

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682179874549-a992a1ef-bcee-4ebe-b8e7-fdfee0f1d6aa.png)

Promise.all()方法接受一个**数组**作为参数，p1、p2、p3都是 Promise 实例

 它的作用是将多个`Promise`包裹在一起形成一个新的`Promise`；

 新的`Promise`状态由包裹的所有`Promise`共同决定：

- 当所有的`Promise`状态变成`fulfilled`状态时，新的`Promise`状态为`fulfilled`，并且会将所有`Promise`的返回值组成一个数组；
-  当有一个`Promise`状态为`reject`时，新的`Promise`状态为`reject`，并且会将第一个`reject`的返回值作为参数；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682177545095-a7ef7c2f-5cdf-450d-9225-91defcc1c6cb.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682177552235-8ee04774-0097-4509-a01c-564174e50a0a.png)



### 8.4. Promise.allSettled()

◼ `all`方法有一个缺陷：当有其中一个`Promise`变成`reject`状态时，新`Promise`就会立即变成对应的`reject`状态。

- 那么对于`resolved`的，以及依然处于`pending`状态的`Promise`，我们是获取不到对应的结果的；

◼ 在`ES11（ES2020）`中，添加了新的`API` `Promise.allSettled`：

- 该方法会在所有的`Promise`都有结果（settled），无论是`fulfilled`，还是`rejected`时，才会有最终的状态；
- 并且这个`Promise`的结果一定是`fulfilled`的；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682177629212-92059e11-f5ba-468d-b0bf-43beeda9df93.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682177617776-a9a91f2b-826b-41e8-adcb-5af0d5bd8ab5.png)

◼ 我们来看一下打印的结果：

- allSettled的结果是一个数组，数组中存放着每一个`Promise`的结果，并且是对应一个对象的；
- 这个对象中包含`status`状态，以及对应的`value`值；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682180007825-cc7f4976-32f0-4a34-ab6b-51b1658415a7.png)

成员对象的status属性的值只可能是字符串fulfilled或字符串rejected，用来区分异步操作是成功还是失败。如果是成功（fulfilled），对象会有value属性，如果是失败（rejected），会有reason属性，对应两种状态时前面异步操作的返回值。



### 8.5. Promise.race()

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682179953373-6b9dd928-312b-4740-b037-b65845251667.png)

◼ 如果有一个`Promise`有了结果，我们就希望决定最终新`Promise`的状态，那么可以使用`race`方法：

- `race`是竞技、竞赛的意思，表示多个`Promise`相互竞争，谁先有结果，那么就使用谁的结果；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682177807353-c567ea38-3d98-4a57-ab3e-abd03fb2762a.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682177816679-8824822a-ba55-41fd-865b-0239a48d8c97.png)



### 8.6. Promise.any()

◼ `any`方法是`ES12`中新增的方法，和`race`方法是类似的：

- `any`方法会等到一个`fulfilled`状态，才会决定新`Promise`的状态；
-  如果所有的`Promise`都是`reject`的，那么也会等到所有的`Promise`都变成`rejected`状态；

◼ 如果所有的`Promise`都是`reject`的，那么会报一个`AggregateError`的错误

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682177889614-3623350e-451c-4f9d-8a95-739e1395b033.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682177897884-f5bb31f7-0029-44a5-a2ff-f87da0bc87b0.png)



### 8.7. Promise.try()

查阅资料了解