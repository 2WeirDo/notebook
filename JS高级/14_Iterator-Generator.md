## 1. 迭代器 (iterator)

### 1.1. 迭代器的含义

◼ 迭代器（iterator），使用户在容器对象（container，例如链表或数组）上遍访的对象，使用该接口无需关心对象的内部实现细节。

◼ 从迭代器的定义我们可以看出来，迭代器是帮助我们对某个数据结构进行遍历的对象。

◼ 在JavaScript中，迭代器也是一个具体的对象，这个对象需要符合迭代器协议（iterator protocol）：

- 迭代器协议定义了产生一系列值（无论是有限还是无限个）的标准方式；
- 在`JavaScript`中这个标准就是一个特定的`next`方法；

◼ `next`方法有如下的要求：

一个无参数或者一个参数的函数，返回一个应当拥有以下两个属性的对象：

1. `done`（boolean）

✓如果迭代器可以产生序列中的下一个值，则为 `false`。（这等价于没有指定 done 这个属性。）

✓ 如果迭代器已将序列迭代完毕，则为 `true`。这种情况下，`value` 是可选的，如果它依然存在，即为迭代结束之后的默认返回值。

1.  `value`

✓ 迭代器返回的任何 `JavaScript` 值。`done `为 `true` 时可省略



代码示例

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682262443209-7fdee35e-58e5-4b13-8a3f-91a1419e462e.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682262462251-b7ce74a2-da43-48bb-83e1-6863b9ac7801.png)





### 1.2. 封装一个函数为数组创建迭代器

```javascript
function createArrayIterator(arr) {
      let index = 0
      return {
        next: function() {
          if (index < arr.length) {
            return { done: false, value: arr[index++] }
          } else {
            return { done: true }
          }
        }
      }
    }
```

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682262983489-4f37e317-5cb1-49f3-b3c9-50efe97233ab.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682263002674-8b4508f4-9e4d-4192-bf7e-7cdf76091803.png)







## 2. 可迭代对象 

### 2.1. 可迭代对象的含义



◼ 什么又是可迭代对象呢？

- 它和迭代器是不同的概念；
- 当一个对象实现了`iterable protocol`协议时，它就是一个可迭代对象；
- 这个对象的要求是必须实现 `@@iterator` 方法，在代码中我们使用 `Symbol.iterator` 访问该属性；



◼ 当然我们要问一个问题，我们转成这样的一个东西有什么好处呢？

- 当一个对象变成一个可迭代对象的时候，就可以进行某些迭代操作；
- 比如 `for...of` 操作时，其实就会调用它的 `@@iterator` 方法；



![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682263403526-de8fb85b-6aef-4a97-ade0-842cefc86cf9.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682263294439-1338cfd5-05ee-4f0d-9e23-f90b122d4fa8.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682263365794-1bf13588-5728-45f7-9e6f-a7804cf0dcc0.png)

可迭代对象必然有一个`[Symbol.iterator]`函数



### 2.2. 数组是一个可迭代对象

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682263565324-e088b9b9-e5f2-4f20-8189-8b8f4d4057eb.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682263578410-b5a6fa7f-c161-4464-8714-5a1db750a540.png)

### 2.3. 可迭代对象优化

可根据具体情况改变迭代器内部

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682264051554-4f5d06df-f578-48c3-be07-45cbeb6a0016.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682264059932-fd01deb9-1111-41a9-bacc-195fca2c9cf6.png)





### 2.4. 原生迭代器对象

◼ 事实上我们平时创建的很多原生对象已经实现了可迭代协议，会生成一个迭代器对象的：

 `String`、`Array`、`Map`、`Set`、`arguments`对象、`NodeList`集合；



数组

```javascript
const set = new Set(["abc", "cba", "nba"]);
for (const item of set) {
  console.log(item);
}
const setIterator = set[Symbol.iterator]();
console.log(setIterator.next());
console.log(setIterator.next());
console.log(setIterator.next());
console.log(setIterator.next());

// abc
// cba
// nba
// { value: 'abc', done: false }
// { value: 'cba', done: false }
// { value: 'nba', done: false }
// { value: undefined, done: true }
```



set

```javascript
const set = new Set(["abc", "cba", "nba"])
for (const item of set) {
  console.log(item)
}
const setIterator = set[Symbol.iterator]()
console.log(setIterator.next())
console.log(setIterator.next())
console.log(setIterator.next())
console.log(setIterator.next())

// abc
// cba
// nba
// { value: 'abc', done: false }
// { value: 'cba', done: false }
// { value: 'nba', done: false }
// { value: undefined, done: true }
```



arguments

```javascript
function foo() {
  for (const arg of arguments) {
    console.log(arg);
  }
}
foo(123, 321, 111, 222);

// 123
// 321
// 111
// 222
```





### 2.5. 可迭代对象的应用

◼ 那么这些东西可以被用在哪里呢？

- JavaScript中语法：`for ...of`、展开语法（spread syntax）、`yield*`（后面讲）、解构赋值（Destructuring_assignment）；
- 创建一些对象时：`new Map([Iterable])`、`new WeakMap([iterable])`、`new Set([iterable])`、`new WeakSet([iterable])`;

- 一些方法的调用：`Promise.all(iterable)、Promise.race(iterable)、Array.from(iterable);`



![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682268268745-3047a45a-6c3b-441a-9eac-cf83cd2e63cd.png)![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682268276093-8c629e43-ed96-44b7-827d-47b958c31621.png)



### 2.6. 自定义类的迭代

```javascript
class Person {
  constructor(name, age, height, friends) {
    this.name = name
    this.age = age
    this.height = height
    this.friends = friends
  }

  // 实例方法
  [Symbol.iterator]() {
    let index = 0
    return {
      next: () => {
        if (index < this.friends.length) {
          return { done: false, value: this.friends[index++] }
        } else {
          return { done: true }
        }
      }
    }
  }
}

const p2 = new Person("kobe", 30, 1.98, ["curry", "james", "aonier", "weide"])

for (const item of p2) {
  console.log(item)
}

// curry
// james
// aonier
// weide
```



### 2.7. 迭代器的中断

◼ 迭代器在某些情况下会在没有完全迭代的情况下中断：

- 比如遍历的过程中通过`break`、`return`、`throw`中断了循环操作；
- 比如在解构的时候，没有解构所有的值；

◼ 那么这个时候我们想要监听中断的话，可以添加`return`方法：



此例中当 break 跳出循环时, 会调用迭代器中的 return 方法 

```javascript
class Person {
  constructor(name, age, height, friends) {
    this.name = name
    this.age = age
    this.height = height
    this.friends = friends
  }

  // 实例方法
  [Symbol.iterator]() {
    let index = 0
    const iterator = {
      next: () => {
        if (index < this.friends.length) {
          return { done: false, value: this.friends[index++] }
        } else {
          return { done: true }
        }
      },
      return: () => {
        console.log("监听到迭代器中断了")
        return { done: true }
      }
    }
    return iterator
  }
}


const p1 = new Person("why", 18, 1.88, ["curry", "kobe", "james", "tatumu"])

for (const item of p1) {
  console.log(item)
  if (item === "kobe") {
    break
  }
}

// curry
// kobe
// 监听到迭代器中断了
```



## 3. 生成器  (generator)



生成器就是一种特殊的迭代器

### 3.1. 生成器函数的基本使用



- `function`后面会跟上符号: `*`
- 代码的执行可以被`yield`控制
- 生成器函数默认在执行时, 返回一个生成器

- - 要想执行函数内部的代码, 需要生成器对象, 调用它的`next`操作
  - 当遇到`yield`时, 就会中断执行





```javascript
// 1.定义了一个生成器函数
function* foo() {
  console.log("1111")
  console.log("2222")
  yield
  console.log("3333")
  console.log("4444")
  yield
  console.log("5555")
  console.log("6666")
}

// 2.调用生成器函数, 返回一个 生成器对象
const generator = foo()
// 调用next方法
generator.next()
// 1111
// 2222
generator.next()
// 3333
// 4444
generator.next()
// 5555
// 6666
```



### 3.2. 生成器函数参数返回值

我们很多时候不希望`next`返回的是一个`undefined`，这个时候我们可以通过`yield`来返回结果；

```javascript
function* foo(name1) {
  console.log("执行内部代码:1111", name1)
  console.log("执行内部代码:2222", name1)
  const name2 = yield "aaaa"
  console.log("执行内部代码:3333", name2)
  console.log("执行内部代码:4444", name2)
  const name3 = yield "bbbb"
  // return "bbbb"
  console.log("执行内部代码:5555", name3)
  console.log("执行内部代码:6666", name3)
  yield "cccc"
  return undefined
}
const generator = foo("next1")
// 调用next方法
console.log(generator.next())  // 先执行, 再 console.log
console.log(generator.next())
console.log(generator.next()) 
console.log(generator.next()) 


// 执行内部代码:1111 next1
// 执行内部代码:2222 next1
// { value: 'aaaa', done: false }
// 执行内部代码:3333 undefined
// 执行内部代码:4444 undefined
// { value: 'bbbb', done: false }
// 执行内部代码:5555 undefined
// 执行内部代码:6666 undefined
// { value: 'cccc', done: false }
// { value: undefined, done: true }
```



**在中间位置直接**`**return**`**后 :** 

```javascript
function* foo(name1) {
  console.log("执行内部代码:1111", name1)
  console.log("执行内部代码:2222", name1)
  const name2 = yield "aaaa"
  console.log("执行内部代码:3333", name2)
  console.log("执行内部代码:4444", name2)
  const name3 = yield "bbbb"
  return "bbbb"   // 注意此处!!😲
  console.log("执行内部代码:5555", name3)
  console.log("执行内部代码:6666", name3)
  yield "cccc"
  return undefined
}
const generator = foo("next1")
console.log(generator.next()) // { done: false, value: "aaaa" }
console.log(generator.next()) // { done: false, value: "bbbb" }
console.log(generator.next()) // { done: true, value: 'bbbb' }
console.log(generator.next()) // { done: true, value: undefined }
console.log(generator.next()) // { done: true, value: undefined }
```



**给函数每次执行的时候, 传入参数 :**

我们在调用`next`函数的时候，可以给它传递参数，那么这个参数会作为上一个`yield`语句的返回值；

第一次next不用传参, 因为foo的参数便是第一次next的参数

```javascript
function* foo(name1) {
  console.log("执行内部代码:1111", name1)
  console.log("执行内部代码:2222", name1)
  const name2 = yield "aaaa"
  console.log("执行内部代码:3333", name2)
  console.log("执行内部代码:4444", name2)
  const name3 = yield "bbbb"
  // return "bbbb"
  console.log("执行内部代码:5555", name3)
  console.log("执行内部代码:6666", name3)
  yield "cccc"
  return undefined
}
const generator = foo("next1")
console.log(generator.next())
console.log(generator.next("next2"))
console.log(generator.next("next3"))
console.log(generator.next())


// 执行内部代码:1111 next1
// 执行内部代码:2222 next1
// { value: 'aaaa', done: false }
// 执行内部代码:3333 next2
// 执行内部代码:4444 next2
// { value: 'bbbb', done: false }
// 执行内部代码:5555 next3
// 执行内部代码:6666 next3
// { value: 'cccc', done: false }
// { value: undefined, done: true }
```



### 3.3. 生成器函数提前结束

利用生成器的 `return `方法提前结束生成器函数

 `return`传值后这个生成器函数就会结束，之后调用`next`不会继续生成值了

```javascript
function* foo(name1) {
  console.log("执行内部代码:1111", name1)
  console.log("执行内部代码:2222", name1)
  const name2 = yield "aaaa"
  console.log("执行内部代码:3333", name2)
  console.log("执行内部代码:4444", name2)
  const name3 = yield "bbbb"
  // return "bbbb"
  console.log("执行内部代码:5555", name3)
  console.log("执行内部代码:6666", name3)
  yield "cccc"

  console.log("最后一次执行")
  return undefined
}

const generator = foo("next1")

// 1.generator.return提前结束函数
console.log(generator.next())
console.log(generator.return("next2"))
console.log("-------------------")
console.log(generator.next("next3"))
console.log(generator.next("next4"))


// 执行内部代码:1111 next1
// 执行内部代码:2222 next1
// { value: 'aaaa', done: false }
// { value: 'next2', done: true }
// -------------------
// { value: undefined, done: true }
// { value: undefined, done: true }
```



`generator.throw`向函数抛出一个异常

```javascript
function* foo(name1) {
  console.log("执行内部代码:1111", name1)
  console.log("执行内部代码:2222", name1)
  const name2 = yield "aaaa"
  console.log("执行内部代码:3333", name2)
  console.log("执行内部代码:4444", name2)
  const name3 = yield "bbbb"
  // return "bbbb"
  console.log("执行内部代码:5555", name3)
  console.log("执行内部代码:6666", name3)
  yield "cccc"

  console.log("最后一次执行")
  return undefined
}

const generator = foo("next1")
console.log(generator.next())
console.log(generator.throw(new Error("next2 throw error")))
console.log("-------------------")
console.log(generator.next("next3"))
console.log(generator.next("next4"))
```

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682266024546-062d786e-741c-461d-93bb-aef9978f52ef.png)





### 3.4. 生成器替代迭代器



```javascript
const names = ["abc", "cba", "nba"]
const nums = [100, 22, 66, 88, 55]

function* createArrayIterator(arr) {
  for (let i = 0; i < arr.length; i++) {
    yield arr[i]
  }
}

  const namesIterator = createArrayIterator(names)
  console.log(namesIterator.next())
  console.log(namesIterator.next())
  console.log(namesIterator.next())
  console.log(namesIterator.next())

  // { value: 'abc', done: false }
  // { value: 'cba', done: false }
  // { value: 'nba', done: false }
  // { value: undefined, done: true }

  const numsIterator = createArrayIterator(nums)
  console.log(numsIterator.next())
  console.log(numsIterator.next())
  console.log(numsIterator.next())
  console.log(numsIterator.next())
  console.log(numsIterator.next())
  console.log(numsIterator.next())

  // { value: 100, done: false }
  // { value: 22, done: false }
  // { value: 66, done: false }
  // { value: 88, done: false }
  // { value: 55, done: false }
  // { value: undefined, done: true }
```







### 3.5. 生成器`yield`语法糖



```javascript
// 1.yield*替换之前的方案

const names = ["abc", "cba", "nba"]
const nums = [100, 22, 66, 88, 55]

function* createArrayIterator(arr) {
  yield* arr
}

const namesIterator = createArrayIterator(names)
console.log(namesIterator.next())
console.log(namesIterator.next())
console.log(namesIterator.next())
console.log(namesIterator.next())

// { value: 100, done: false }
// { value: 22, done: false }
// { value: 66, done: false }
// { value: 88, done: false }
// { value: 55, done: false }
// { value: undefined, done: true }
```



`yield`替换类中的实现

```javascript
class Person {
  constructor(name, age, height, friends) {
    this.name = name
    this.age = age
    this.height = height
    this.friends = friends
  }

  // 实例方法
  *[Symbol.iterator]() {
    yield* this.friends
  }
}

const p = new Person("why", 18, 1.88, ["kobe", "james", "curry"])

for (const item of p) {
  console.log(item)
}
// kobe
// james
// curry


const pIterator = p[Symbol.iterator]()
console.log(pIterator.next())
console.log(pIterator.next())
console.log(pIterator.next())
console.log(pIterator.next())

// { value: 'kobe', done: false }
// { value: 'james', done: false }
// { value: 'curry', done: false }
// { value: undefined, done: true }
```





## 4. 异步处理方案  🔥



### 4.1. 查看案例需求 

1.  发送一次网络请求, 等待这次网络请求的结果  
2. 发送第二次网络请求, 依赖第一次的结果  
3. 发送第三次网络请求, 依赖第二次的结果
4. 以此类推

注意要等待上一次请求结果出来后再进行下一次网络请求



封装请求的方法

```javascript
function requestData(url) {
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          resolve(url)
        }, 2000)
      })
    }
```



发送一次网络请求

```javascript
requestData("http://why").then(res => {
  console.log("res:", res)
})
```



### 4.2. 方式一: 层层嵌套 (回调地狱 callback hell)

```javascript
function getData() {
  // 1.第一次请求
  requestData("why").then(res1 => {
    console.log("第一次结果:", res1)

    // 2.第二次请求
    requestData(res1 + "kobe").then(res2 => {
      console.log("第二次结果:", res2)

      // 3.第三次请求
      requestData(res2 + "james").then(res3 => {
        console.log("第三次结果:", res3)
      })
    })
  })
}

// 第一次结果: why
// 第二次结果: whykobe
// 第三次结果: whykobejames
```



### 4.3. 方式二: 使用`Promise`进行重构 (解决回调地狱)

```javascript
// 链式调用
function getData() {
  requestData("why").then(res1 => {
    console.log("第一次结果:", res1)
    return requestData(res1 + "kobe")  // 返回一个promise , 则由这个promise内 solve 返回
  }).then(res2 => {
    console.log("第二次结果:", res2)
    return requestData(res2 + "james")
  }).then(res3 => {
    console.log("第三次结果:", res3)
  })
}

// 第一次结果: why
// 第二次结果: whykobe
// 第三次结果: whykobejames
```



### 4.4. 方式三: 利用`Generator`进行操作

```javascript
function* getData() {
  const res1 = yield requestData("why")
  console.log("res1:", res1)

  const res2 = yield requestData(res1 + "kobe")
  console.log("res2:", res2)

  const res3 = yield requestData(res2 + "james")
  console.log("res3:", res3)
}

const generator = getData()
generator.next().value.then(res1 => {
  generator.next(res1).value.then(res2 => {
    generator.next(res2).value.then(res3 => {
      generator.next(res3)
    })
  })
})

// 第一次结果: why
// 第二次结果: whykobe
// 第三次结果: whykobejames
```

两个问题

1.  我们不能确定到底需要调用几层的`Promise`关系；
2. 如果还有其他需要这样执行的函数，我们应该如何操作呢？

- 所以，我们可以封装一个工具函数`execGenerator`自动执行生成器函数：

自动化执行生成器函数

```javascript
// (了解)

function* getData() {
  const res1 = yield requestData("why")
  console.log("res1:", res1)

  const res2 = yield requestData(res1 + "kobe")
  console.log("res2:", res2)

  const res3 = yield requestData(res2 + "james")
  console.log("res3:", res3)
}

function execGenFn(genFn) {
  // 1.获取对应函数的generator
  const generator = genFn()
  // 2.定义一个递归函数
  function exec(res) {
    // result -> { done: true/false, value: 值/undefined }
    const result = generator.next(res)
    if (result.done) return
    result.value.then(res => {
      exec(res)
    })
  }
  // 3.执行递归函数
  exec()
}

execGenFn(getData)
```

### 4.5. 方式四: `async/await`的解决方案

最优解

```javascript
async function getData() {
  const res1 = await requestData("why")
  console.log("第一次结果:", res1)

  const res2 = await requestData(res1 + "kobe")
  console.log("第二次结果:", res2)

  const res3 = await requestData(res2 + "james")
  console.log("第三次结果:", res3)
}

const generator = getData()

// 第一次结果: why
// 第二次结果: whykobe
// 第三次结果: whykobejames
```



以上方式返回结果都是一样的 : 

```javascript
// 第一次结果: why
// 第二次结果: whykobe
// 第三次结果: whykobejames
```