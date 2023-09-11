## 1. 什么是函数的类型

```typescript
function foo(arg: number): number {
  return 123
}

// foo本身也是一个标识符, 也应该有自己的类型
const foo: any = (arg: number): number => {
  return 123
}
```

这里foo的函数类型就是any

## 2. 函数类型的表示方法

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1691837808405-ead60f59-0f8c-4d26-8ded-83466045f0b1.png)

我们可以编写函数类型的表达式（Function Type Expressions），来表示函数类型；

```typescript
// 方案一: 函数类型表达式 function type expression
// 格式: (参数列表) => 返回值
type BarType = (num1: number) => number
const bar: BarType = (arg: number): number => {
  return 123
}
```



### 2.1. 函数类型表达式的练习

```typescript
type CalcType = (num1: number, num2: number) => number
// 在某些语言中，可能参数名称num1和num2是可以省略，但是TypeScript是不可以的：

// 1.函数的定义
function calc(calcFn: CalcType) {
  const num1 = 10
  const num2 = 20
  const res = calcFn(num1, num2)
  console.log(res)
}


// 2.函数的调用
function sum(num1: number, num2: number) {
  return num1 + num2
}

function foo(num1: number) {
  return num1
}
calc(sum)
calc(foo)

function mul(num1: number, num2: number) {
  return num1 * num2
}
calc(mul)

// 3.使用匿名函数
calc(function(num1, num2) {
  return num1 - num2
})
```



### 2.2. 函数类型参数的个数

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1691822416870-20a8b758-2c15-4e5a-ba29-98c866aeb3dd.png)

`TypeScript`对于传入的函数类型的**多余的参数会被忽略掉**

```typescript
// TypeScript对于传入的函数类型的多余的参数会被忽略掉(the extra arguments are simply ignored.)
type CalcType = (num1: number, num2: number) => number
function calc(calcFn: CalcType) {
  calcFn(10, 20)
}

calc(function(num) {
  // 多余的参数会被忽略掉
  return 123
})

// forEach栗子:
const names = ["abc", "cba", "nba"]
names.forEach(function(item) {
  // 不用写完, 比如这里就只写了item
  console.log(item.length)
})

// TS对于很多类型的检测报不报错, 取决于它的内部规则
// TS版本在不断更新: 在进行合理的类型检测的情况, 让ts同时更好用(好用和类型检测之间找到一个平衡)
// 举一个栗子:
interface IPerson {
  name: string
  age: number
}

// typescript github issue, 
// 如果对象是新鲜的, 就要保持和IPerson一样, 如果不是新鲜, (已经定义) 就可以不报错
const p = {
  name: "why",
  age: 18,
  height: 1.88,
  address: "广州市"
}

const info: IPerson = p
```



### 2.3. 调用签名

开发中如何选择:

* 如果**只是描述函数类型本身**(函数可以被调用), 使用**函数类型表达式**(Function Type Expressions)
* 如果在描述函数作为对象**可以被调用, 同时也有其他属性**时, 使用函数**调用签名**(Call Signatures)

```typescript
// 1.函数类型表达式
type BarType = (num1: number) => number
// 这里是 => 号

// 2.函数的调用签名(从对象的角度来看待这个函数, 也可以有其他属性)
interface IBar {
  name: string
  age: number
  // 函数可以调用: 函数调用签名
  // 注意这里是冒号
  (num1: number): number
}

const bar: IBar = (num1: number): number => {
  return 123
}

bar.name = "aaa"
bar.age = 18
bar(123)


// 开发中如何选择:
// 1.如果只是描述函数类型本身(函数可以被调用), 使用函数类型表达式(Function Type Expressions)
// 2.如果在描述函数作为对象可以被调用, 同时也有其他属性时, 使用函数调用签名(Call Signatures)
```



### 2.4. 构造签名

用的不多, 理解即可

```typescript
class Person {
}

interface ICTORPerson {
  new (): Person
}

function factory(fn: ICTORPerson) {
  const f = new fn()
  return f
}

factory(Person)
// Person本身是一个类, 同时也是一个构造函数, 所以可以直接传在factory中
```



## 3. 函数的参数

### 3.1. 可选参数

可选参数类型是什么?

 `number` | `undefined` 联合类型

```typescript
// y就是一个可选参数
// 可选参数类型是什么? number | undefined 联合类型
function foo(x: number, y?: number) {
  // 类型缩小
  if (y !== undefined) {
    console.log(y + 10)
  }
}

foo(10)
foo(10, 20)
```

可选类型需要在必传参数的后面



### 3.2. 参数默认值

* 有默认值的情况下, 参数的类型注解可以省略
* 有默认值的参数, 是可以接收一个`undefined`的值

```typescript
// 函数的参数可以有默认值
// 1.有默认值的情况下, 参数的类型注解可以省略
// 2.有默认值的参数, 是可以接收一个undefined的值
function foo(x: number, y = 100) {
  console.log(y + 10)
}

foo(10)
foo(10, undefined)
foo(10, 55)
```



### 3.3. 剩余参数

```typescript
function foo(...args: (string | number)[]) {
}

foo(123, 321)
foo("abc", 111, "cba")
```



## 4. 函数重载

需求: 只能将两个数字/两个字符串进行相加

 **一般是编写两个或者以上的重载签名，再去编写一个通用的函数以及实现；**

```typescript
// 需求: 只能将两个数字/两个字符串进行相加
// 案例分析: any实现
// function add(arg1, arg2) {
//   return arg1 + arg2
// }

// add(10, 20)
// add("abc", "cba")
// add({aaa: "aaa"}, 123)


// 1.实现两个函数  (麻烦)
// function add1(num1: number, num2: number) {
//   return num1 + num2
// }

// function add2(str1: string, str2: string) {
//   return str1 + str2
// }

// add1(10, 20)
// add2("abc", "cba")


// 2.错误的做法: 联合类型是不可以的, 无法判断具体是什么类型
// function add(arg1: number|string, arg2: number|string) {
//   return arg1 + arg2
// }


// 3.TypeScript中函数的重载写法
// 3.1.先编写重载签名
function add(arg1: number, arg2: number): number
function add(arg1: string, arg2: string): string

// 3.2.编写通用的函数实现
function add(arg1: any, arg2: any): any {
  return arg1 + arg2
}

add(10, 20)
add("aaa", "bbb")

// 通用函数不能被调用
// add({name: "why"}, "aaa")
// add("aaa", 111)
```



**联合类型实现**

需求 : 定义一个函数，可以传入字符串或者数组，获取它们的长度。

```typescript
// 需求 : 定义一个函数，可以传入字符串或者数组，获取它们的长度。

// 1.普通的实现
function getLength(arg) {
  return arg.length
}

// 2.函数的重载
function getLength(arg: string): number
function getLength(arg: any[]): number
function getLength(arg) {
  return arg.length
}


// 3.联合类型实现(可以使用联合类型实现的情况, 尽量使用联合类型)
function getLength(arg: string | any[]) {
  return arg.length
}

// 4.对象类型实现
function getLength(arg: { length: number }) {
  return arg.length
}


getLength("aaaaa")
getLength(["abc", "cba", "nba"])
getLength({ length: 100 })
```

可以使用联合类型实现的情况, 尽量使用联合类型



## 5. 函数中this类型



### 5.1. 默认类型

```typescript
// 在没有对TS进行特殊配置的情况下, this是any类型

// 1.对象中的函数中的this
const obj = {
  name: "why",
  studying: function() {
    // 默认情况下, this是any类型
    console.log(this.name.length, "studying")
  }
}

obj.studying()
// obj.studying.call({})


// 2.普通的函数
function foo() {
  console.log(this)
}

export {}
```

配置tsconfig.json :

```
tsc --init
```

### 5.2. 明确类型

配置`tsconfig.json`之后

在设置了`noImplicitThis`为`true`时， `TypeScript`会根据上下文推导`this`，但是在不能正确推导时，就会报错，需要我们明确的指定`this`

◼ 如何指定呢？函数的第一个参数类型：

*  函数的第一个参数我们可以根据该函数之后被调用的情况，用于声明`this`的类型（名称必须叫this）；
*  在后续调用函数传入参数时，从第二个参数开始传递的，`this`参数会在编译后被抹除；

```typescript
// 在设置配置选项(编译选项compilerOptions, noImplicitThis设置为true, 不允许模糊的this存在)

// 1.对象中的函数中的this
const obj = {
  name: "why",
  studying: function(this: {}) {
    // 默认情况下, this是any类型
    console.log(this, "studying")
  }
}

// obj.studying()
obj.studying.call({})


// 2.普通的函数
function foo(this: { name: string }, info: {name: string}) {
  console.log(this, info)
}

foo.call({ name: "why" }, { name: "kobe" })
```



## 6. this的内置工具的使用

1. `ThisParameterType`: 获取FooType类型中this的类型
2. `OmitOmitThisParameter`: 删除this参数类型, 剩余的函数类型
3. `ThisType`: 用于绑定一个上下文的this (了解)

```typescript
function foo(this: { name: string }, info: {name: string}) {
  console.log(this, info)
}

type FooType = typeof foo

// 1.ThisParameterType: 获取FooType类型中this的类型
type FooThisType = ThisParameterType<FooType>


// 2.OmitOmitThisParameter: 删除this参数类型, 剩余的函数类型
type PureFooType = OmitThisParameter<FooType>


// 3.ThisType: 用于绑定一个上下文的this
interface IState {
  name: string
  age: number
}

interface IStore {
  state: IState
  eating: () => void
  running: () => void
}

const store: IStore & ThisType<IState> = {
  state: {
    name: "why",
    age: 18
  },
  eating: function() {
    console.log(this.name)
  },
  running: function() {
    console.log(this.name)
  }
}

store.eating.call(store.state)


export {}
```





## 7. coderwhy关于ts学习等级划分

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1691827114731-1b1725c9-bdf8-4c9d-afdb-a233bc8d3384.png)