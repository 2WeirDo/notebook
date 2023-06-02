## 1. 异步函数

### 1.1. 异步函数的写法 (`async`)

`async`关键字用于声明一个异步函数：

异步函数也是跟普通函数一样**立即执行**

 `async`是`asynchronous`单词的缩写，异步、非同步；

 `sync`是`synchronous`单词的缩写，同步、同时；

异步函数的内部代码执行过程和普通的函数是一致的，默认情况下也是会被同步执行



异步函数的写法

```javascript
// 异步函数
const bar = async function() {}

const baz = async () => {}

async function foo() {
}

class Person {
    async running() {}
}
```



### 1.2. 异步函数的返回值

返回值是一个`promise`对象 

异步函数（用`async`关键字声明的函数）的返回值总是一个`Promise`对象。当异步函数内的代码执行完毕，且没有遇到错误时，`Promise`将以`resolve`状态结束，携带返回值；如果在执行过程中遇到错误，则`Promise`会以`reject`状态结束，携带错误信息。

`return`后的值 是 作为`then`的参数进行传递的

-  异步函数有返回值时，和普通函数会有区别：

- -  情况一：异步函数也可以有返回值，但是异步函数的返回值相当于被包裹到`Promise.resolve`中；
  - 情况二：如果我们的异步函数的返回值是`Promise`，状态由会由`Promise`决定；
  - 情况三：如果我们的异步函数的返回值是一个对象并且实现了`thenable`，那么会由对象的`then`方法来决定；



返回一个普通的值

```javascript
// 返回值的区别

async function foo2() {
  return ["abc", "cba", "nba"]  
}
foo2().then(res => {
  console.log("res:", res)
})  
// res: [ 'abc', 'cba', 'nba' ]
```



返回一个promise

```javascript
// 返回值的区别
async function foo2() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve("aaa")
    }, 3000)
  })
}
foo2().then(res => {
  console.log("res:", res)
})  

// res: aaa
```



返回一个thenable对象

```javascript
// 返回值的区别

async function foo2() {
  return {
    then: function (resolve, reject) {
      resolve("bbb");
    },
  };
}
foo2().then((res) => {
  console.log("res:", res);
});

// res: bbb
```





### 1.3. 异步函数的异常



什么情况下异步函数的结果是`rejected`?



进行如下处理 : 

```javascript
async function foo() {
  console.log("---------1")
  console.log("---------2")
  console.log("---------3")
  return new Promise((resolve, reject) => {
    reject("err rejected")
  })
}

foo().then(res => {
  console.log("res:", res)
}).catch(err => {
  console.log("coderwhy err:", err)
  console.log("继续执行其他的逻辑代码")
})

// ---------1
// ---------2
// ---------3
// coderwhy err: err rejected
// 继续执行其他的逻辑代码
```



如果异步函数中有抛出异常(产生了错误), 这个异常不会被立即浏览器处理. (可以算是异步函数的好处 ? )

如果我们在`async`中抛出了异常，那么程序它并不会像普通函数一样报错，而是会作为`Promise`的`reject`来传递；



可以在用 `catch` 捕获异常

```javascript
async function foo() {
    console.log("---------1")
    console.log("---------2")
    throw new Error("coderwhy async function error")
    console.log("---------3")
    return 123
}
foo().then(res => {
    console.log("res:", res)
}).catch(err => {
    console.log("coderwhy err:", err)
    console.log("继续执行其他的逻辑代码")
})
```

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682439692132-c95f2107-b671-4076-9aea-5a5ce317667b.png)



### 1.4. `await`关键字使用

必须在异步函数中使用



`await`关键字有什么特点呢？



- 通常使用`await`是后面会跟上一个表达式，这个表达式会返回一个`Promise`；
- 那么`await`会等到`Promise`的状态变成`fulfilled`状态，之后继续执行异步函数；



```javascript
// bar 是某函数, 这个代码只是看看而已
async function foo() {
    console.log("-------")
    // await后续返回一个Promise, 那么会等待Promise有结果之后, 才会继续执行后续的代码
    const res1 = await bar()
    console.log("await后面的代码:", res1)
    const res2 = await bar()
    console.log("await后面的代码:", res2)
    console.log("+++++++")
}

foo()
```



同异步函数的返回值一样 ----



◼ 如果`await`后面是一个普通的值，那么会直接返回这个值；

◼ 如果`await`后面是一个`thenable`的对象，那么会根据对象的`then`方法调用来决定后续的值；

◼ 如果`await`后面的表达式，返回的`Promise`是`reject`的状态，那么会将这个`reject`结果直接作为函数的`Promise`的`reject`值；

### 1.5. `await`处理异步请求

这是之前 (上一节结尾) 我们的例子

```javascript
function requestData(url) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve(url)
            // reject("error message")
        }, 2000);
    })
}

async function getData() {
    const res1 = await requestData("why")
    console.log("res1:", res1)
    const res2 = await requestData(res1 + "kobe")
    console.log("res2:", res2)
}

getData().catch(err => {
    console.log("err:", err)
})

// res1: why
// res2: whykobe
```







## 2. 单线程



### 2.1. 进程和线程



◼ 线程和进程是操作系统中的两个概念：

- 进程（process）：计算机已经运行的程序，是操作系统管理程序的一种方式；
- 线程（thread）：操作系统能够运行运算调度的最小单位，通常情况下它被包含在进程中；



◼ 听起来很抽象，这里还是给出我的解释：

- 进程：我们可以认为，启动一个应用程序，就会默认启动一个进程（也可能是多个进程）；
- 线程：每一个进程中，都会启动至少一个线程用来执行程序中的代码，这个线程被称之为主线程；
- 所以我们也可以说进程是线程的容器；



◼ 再用一个形象的例子解释：

- 操作系统类似于一个大工厂；
- 工厂中里有很多车间，这个车间就是进程；
- 每个车间可能有一个以上的工人在工厂，这个工人就是线程



### 2.2. 浏览器中的`JavaScript`线程



◼ 浏览器是一个进程吗，它里面只有一个线程吗？

-  目前多数的浏览器其实都是多进程的，当我们打开一个`tab`页面时就会开启一个新的进程，这是为了防止一个页面卡死而造成所有页面无法响应，整个浏览器需要强制退出；
- 每个进程中又有很多的线程，其中包括执行`JavaScript`代码的线程；



◼ `JavaScript`的代码执行是在一个单独的线程中执行的：

- 这就意味着`JavaScript`的代码，在同一个时刻只能做一件事；
- 如果这件事是非常耗时的，就意味着当前的线程就会被阻塞；



◼ 所以真正耗时的操作，实际上并不是由`JavaScript`线程在执行的：

- 浏览器的每个进程是多线程的，那么其他线程可以来完成这个耗时的操作；
- 比如网络请求、定时器，我们只需要在特性的时候执行应该有的回调即可；



### 2.3. 代码顺序如何执行



```javascript
function bar() {
  console.log("bar function")
}

function foo() {
  console.log("foo function")
  // 创建一个定时器
  setTimeout(() => {
    console.log("setTimeout")
  }, 0);
  bar()
}

foo()

// foo function
// bar function
// setTimeout
```

setTimeout() 它不是由JavaScript线程进行数数的 , 它是交给浏览器的其它线程进行操作, 操作完放入宏任务队列中等待执行栈为空时再进行任务队列的任务执行





## 3. 微任务和宏任务区别(事件循环)🔥



`javascrip`t**的异步任务又被分为宏任务和微任务。**

◼ 事件循环中并非只维护着一个队列，事实上是有两个队列：

-  宏任务队列（`macrotask queue`）：

- - `ajax`、`setTimeout`、`setInterval`、`DOM`监听、`UI Rendering`等

- 微任务队列（`microtask queue`）：

- - `Promise`的`then`回调、 `Mutation Observer API`、`queueMicrotask()`等



`Promise.then() `---微任务

`async/await `---`Promise`的语法糖 ---微任务

`setTimeout()` ---宏任务

`setInterval() `---宏任务

...（还有更多，不常见的）



事件循环的含义  --- 我觉得表述的还可以

> 当有异步任务被压入异步任务队列时候，`javascript`会将这些异步任务分为宏任务和微任务两个新的队列。然后，在所有同步任务执行完毕之后，异步任务会优先执行所有已经存在任务队列中的微任务。在所有的微任务执行完毕之后，再去宏任务队列中执行一个（注意是一个）宏任务，执行完一个宏任务之后会再去微任务队列中检查是否有新的微任务，有则全部执行，再回到宏任务队列执行一个宏任务，以此循环。这一套流程，就是`事件循环（event loop）`





**每次要进行一个新的宏任务时, 先将微任务队列中的任务清空再进行.**

```javascript
console.log("script start")

function bar() {
  console.log("bar function")
}

function foo() {
  console.log("foo function")
  bar()
}

foo()

setTimeout(() => {
  console.log("setTimeout0") 
}, 0)

setTimeout(() => {
  console.log("setTimeout1")
}, 0)

// Promise中的then的回调也会被添加到队列中, 注意是微任务队列
console.log("1111111")
new Promise((resolve, reject) => {
  console.log("2222222")
  console.log("-------1")
  console.log("-------2")
  resolve()
  console.log("-------3")
}).then(res => {
  console.log("then传入的回调: res", res)
})
console.log("3333333")

console.log("script end")


// script start
// foo function
// bar function
// 1111111
// 2222222
// -------1
// -------2
// -------3
// 3333333
// script end
// then传入的回调: res undefined
// setTimeout0
// setTimeout1
```



### 3.1. 代码执行顺序

全局 => 微任务队列 => 宏任务队列

### 3.2. 面试题一

可以画图理解 , 执行顺序/微任务队列/宏任务队列/执行栈

```javascript
console.log("script start")

setTimeout(function () {
    console.log("setTimeout1");
    new Promise(function (resolve) {
        resolve();
    }).then(function () {
        new Promise(function (resolve) {
            resolve();
        }).then(function () {
            console.log("then4");
        });
        console.log("then2");
    });
});

new Promise(function (resolve) {
    console.log("promise1");
    resolve();
}).then(function () {
    console.log("then1");
});

setTimeout(function () {
    console.log("setTimeout2");
});

console.log(2);

queueMicrotask(() => {
    console.log("queueMicrotask1")
});

new Promise(function (resolve) {
    resolve();
}).then(function () {
    console.log("then3");
});

console.log("script end")


// script start
// promise1
// 2
// script end
// then1
// queueMicrotask1
// then3
// setTimeout1
// then2
// then4
// setTimeout2
```



### 3.3. 面试题二

```javascript
//第一个宏任务
setTimeout(() => {
  console.log(1); //宏任务中的同步任务
  Promise.resolve().then(() => { console.log(7) }) //宏任务中的微任务
}, 0);  //异步任务 - 宏任务

console.log(2);   //同步任务

Promise.resolve().then(() => { console.log(3) }) //异步任务 - 微任务

//第二个宏任务
setTimeout(() => { 
  console.log(8); //宏任务中的同步任务
  setTimeout(() => { console.log(5) }, 0)      //宏任务中的宏任务 第四个宏任务
}, 0);

//第三个宏任务
setTimeout(() => { 
  Promise.resolve().then(() => { console.log(4) })  //宏任务中的微任务
}, 0);

console.log(6);   //同步任务

//  2 6 3 1 7 8 4 5
```

### 3.4. 面试题三 (自己凑的)

`promise`调用`then`调度版本

```javascript
console.log("script start")

function requestData(url) {
    console.log("requestData")
    return new Promise((resolve) => {
        setTimeout(() => {
            console.log("setTimeout")
            resolve(url)
        }, 2000);
    })
}

// promise 在调用then调度
function getData(){
    console.log("getData start");
    requestData("why").then(res => {
        console.log("then1-res:", res);
    })
    console.log("getData end");
}

getData()

console.log("script end")

// script start
// getData start
// requestData
// getData end
// script end
// setTimeout
// then1-res: why
```



`await/async`版本

```javascript
console.log("script start")

function requestData(url) {
    console.log("requestData")
    return new Promise((resolve) => {
        setTimeout(() => {
            console.log("setTimeout")
            resolve(url)
        }, 2000);
    })
}

// await/async
async function getData() {
    console.log("getData start")
    const res = await requestData("why")

    console.log("then1-res:", res)
    console.log("getData end")
}

getData()

console.log("script end")

// script start
// getData start
// requestData
// script end
// setTimeout
// then1-res: why
// getData end
```



### 3.5. 面试题四

```javascript
async function async1 () {
    console.log('async1 start')
    await async2();
    console.log('async1 end')
}

async function async2 () {
    console.log('async2')
}

console.log('script start')

setTimeout(function () {
    console.log('setTimeout')
}, 0)

async1();

new Promise (function (resolve) {
    console.log('promise1')
    resolve();
}).then (function () {
    console.log('promise2')
})

console.log('script end')


// script start
// async1 start
// async2
// promise1
// script end
// async1 end
// promise2
// setTimeout
```









## 4. 异常处理

当我们平时在进行一些操作时 , 遇到错误后会造成后续的代码全部不能执行, 这是很危险的

因此我们需要抛出异常



### 4.1. `throw`抛出异常

JavaScript内部封装了一个`Error`类, 我们为了方便可以直接用

```javascript
// throw抛出一个异常
// 1.函数中的代码遇到throw之后, 后续的代码都不会执行
// 2.throw抛出一个具体的错误信息
function foo() {
    console.log("foo function1")
    // 1.number/string/boolean
    // throw "反正就是一个错误"

    // 2.抛出一个对象
    // throw { errMessage: "我是错误信息", errCode: -1001 }
    // throw new HYError("错误信息", -1001)

    // 3.Error类: 错误函数的调用栈以及位置信息
    throw new Error("我是错误信息")

    console.log("foo function2")
    console.log("foo function3")
    console.log("foo function4")
}

function bar() {
    foo()
}

bar()
```

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682443841859-0bf71915-84ad-4c99-ac32-e011fe557258.png)



◼ 我们会发现在之前的代码中，一个函数抛出了异常，调用它的时候程序会被强制终止：

- 这是因为如果我们在调用一个函数时，这个函数抛出了异常，但是我们并没有对这个异常进行处理，那么这个异常会继续传递到上一个函数调用中；
- 而如果到了最顶层（全局）的代码中依然没有对这个异常的处理代码，这个时候就会报错并且终止程序的运行；



### 4.2. 异常的捕获方式

没有异常

```javascript
function foo() {
    console.log("foo function1")
    console.log("foo function2")
    console.log("foo function3")
    console.log("foo function4")
}

function test() {
    try {
        foo()
        console.log("try后续的代码")
    } catch(error) {
        console.log("catch中的代码")
    } finally {
        console.log("finally代码")
    }
}

function bar() {
    test()
}

bar()
console.log("--------")


// foo function1
// foo function2
// foo function3
// foo function4
// try后续的代码
// finally代码
// --------
```



存在异常

```javascript
 function foo() {
      console.log("foo function1")
      throw new Error("我是错误信息")
      console.log("foo function2")
      console.log("foo function3")
      console.log("foo function4")
    }

    function test() {
      // 自己捕获了异常的话, 那么异常就不会传递给浏览器, 那么后续的代码可以正常执行
      try {
        foo()
        console.log("try后续的代码")
      } catch(error) {
        console.log("catch中的代码")
        console.log(error)
      } finally {
        console.log("finally代码")
      }
    }

    function bar() {
      test()
    }

    bar()

    console.log("--------")
```

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682444088441-1a23d55c-425e-4854-8554-def6202dcdd4.png)

`finally`表示最终一定会被执行的代码