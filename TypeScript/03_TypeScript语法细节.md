## 1. 联合类型使用

```
|
```

 联合类型是由两个或者多个其他类型组成的类型；

 表示可以是这些类型中的任何一个值；

 联合类型中的每一个类型被称之为联合成员（union's members）；

```typescript
// 举个栗子: 打印id
function printID(id: number | string) {
  console.log("您的ID:", id)

  // 类型缩小
  if (typeof id === "string") {
    console.log(id.length)
  } else {
    console.log(id)
  }
}

printID("abc")
printID(123)
```



## 2. 类型别名使用

关键词 : `type`

```typescript
// 类型别名: type
type MyNumber = number
const age: MyNumber = 18

// 给ID的类型起一个别名
type IDType = number | string

function printID(id: IDType) {
  console.log(id)
}


// 打印坐标
type PointType = { x: number, y: number, z?: number }
function printCoordinate(point: PointType) {
  console.log(point.x, point.y, point.z)
}
```

## 3. 接口声明使用

```typescript
type PointType = {
  x: number
  y: number
  z?: number
}

// 接口: interface
// 声明的方式
interface PointType2 {
  x: number
  y: number
  z?: number
}

function printCoordinate(point: PointType2) {}
```

## 4. 别名和接口的区别

* 区别一: `type`类型使用范围更广, 接口类型只能用来声明对象
* 区别二: 在声明对象时, `interface`可以多次声明
* 区别三: `interface`支持继承的

```typescript
// 1.区别一: type类型使用范围更广, 接口类型只能用来声明对象
type MyNumber = number
type IDType = number | string


// 2.区别二: 在声明对象时, interface可以多次声明
// 2.1. type不允许两个相同名称的别名同时存在
// type PointType1 = {
//   x: number
//   y: number
// }

// type PointType1 = {
//   z?: number
// }


// 2.2. interface可以多次声明同一个接口名称
interface PointType2 {
  x: number
  y: number
}

interface PointType2 {
  z: number
}

const point: PointType2 = {
  x: 100,
  y: 200,
  z: 300
}


// 3.interface支持继承的
interface IPerson {
  name: string
  age: number
}

interface IKun extends IPerson {
  kouhao: string
}

const ikun1: IKun = {
  kouhao: "你干嘛, 哎呦",
  name: "kobe",
  age: 30
}

// 4.interface可以被类实现(TS面向对象时候再讲)
// class Person implements IPerson {
// }


// 总结: 如果是非对象类型的定义使用type, 如果是对象类型的声明那么使用interface
```

总结: 如果是非对象类型的定义使用`type`, 如果是对象类型的声明那么使用`interface`

## 5. 交叉类型的使用

在开发中，我们进行交叉时，通常是**对对象类型进行交叉的**

```typescript
// 回顾: 联合类型
type ID = number | string
const id1: ID = "abc"
const id2: ID = 123

// 交叉类型: 两种(多种)类型要同时满足
// type NewType = number & string // 没有意义

interface IKun {
  name: string
  age: number
}

interface ICoder {
  name: string
  coding: () => void
}

type InfoType = IKun & ICoder
// 交叉类型一般用于交叉对象

const info: InfoType = {
  name: "why",
  age: 18,
  coding: function() {
    console.log("coding")
  }
}
```

## 6. 类型断言使用

类型断言的规则: 断言只能断言成**更加具体**的类型, 或者**不太具体**(`any`/`unknown`) 类型

```typescript
// 获取DOM元素 <img class="img"/>
// const imgEl = document.querySelector(".img")
// if (imgEl !== null) { // 类型缩小
//   imgEl.src = "xxx"
//   imgEl.alt = "yyy"
// }

// 当我们通过获取类名获取元素时我们不知道此元素是什么类型, 
//因此我们使用类型断言来确定是某一个元素因而使用此元素的相关属性
const imgEl = document.querySelector(".img") as HTMLImageElement
imgEl.src = "xxx"
imgEl.alt = "yyy"


// 类型断言的规则: 断言只能断言成更加具体的类型, 或者 不太具体(any/unknown) 类型
const age: number = 18
// 错误的做法
// const age2 = age as string

// TS类型检测来说是正确的, 但是这个代码本身不太正确
// const age3 = age as any
// const age4 = age3 as string
// console.log(age4.split(" "))
```

## 7. 非空类型断言

非空断言使用的是 `!` ，表示可以确定某个标识符是有值的，**跳过**`**ts**`**在编译阶段对它的检测；**

```typescript
// 定义接口
interface IPerson {
  name: string
  age: number
  friend?: {
    name: string
  }
}

const info: IPerson = {
  name: "why",
  age: 18
}
// 注意这个info没有friend属性

// 访问属性: 可选链: ?.
console.log(info.friend?.name)

// 属性赋值:
// 解决方案一: 类型缩小
if (info.friend) {
  info.friend.name = "kobe"
}

// 解决方案二: 非空类型断言(有点危险, 只有确保friend一定有值的情况, 才能使用)
info.friend!.name = "james"

export {}
```

可选链只能解决访问问题, 不能解决赋值问题

(可选链不能放在 `=` 左边)



## 8. 字面量类型使用

```typescript
// 1.字面量类型的基本上
const name: "why" = "why"
let age: 18 = 18

// 2.将多个字面量类型联合起来 |
type Direction = "left" | "right" | "up" | "down"
const d1: Direction = "left"
```

封装请求方法

```typescript
// 例子: 封装请求方法
type MethodType = "get" | "post"
function request(url: string, method: MethodType) {
}

request("http://codercba.com/api/aaa", "post")

// TS细节
// const info = {
//   url: "xxxx",
//   method: "post"
// }

// 下面的做法是错误: info.method获取的是string类型
// request(info.url, info.method)

// 解决方案一: info.method进行类型断言
request(info.url, info.method as "post")

// 解决方案二: 直接让info对象类型是一个字面量类型
const info2: { url: string, method: "post" } = {
  url: "xxxx",
  method: "post"
}

// 解决方案三 : 通过 as const 将属性变为字面量类型
const info2 = {
  url: "xxxx",
  method: "post"
} as const

// 此处的 as const 可以将info2的属性全部变为字面量类型, 因此method就变为了post类型
// xxx 本身就是一个string
request(info2.url, info2.method)
```



## 9. 类型缩小的使用

* `typeof`: 使用的最多
* `===`/`!==`: 方向的类型判断
* `instanceof` : 判断是否是其实例
* `in`: 判断是否有某一个属性

```typescript
// 1.typeof: 使用的最多
function printID(id: number | string) {
  if (typeof id === "string") {
    console.log(id.length, id.split(" "))
  } else {
    console.log(id)
  }
}


// 2.===/!==: 方向的类型判断
type Direction = "left" | "right" | "up" | "down"
function switchDirection(direction: Direction) {
  if (direction === "left") {
    console.log("左:", "角色向左移动")
  } else if (direction === "right") {
    console.log("右:", "角色向右移动")
  } else if (direction === "up") {
    console.log("上:", "角色向上移动")
  } else if (direction === "down") {
    console.log("下:", "角色向下移动")
  }
}


// 3. instanceof: 传入一个日期, 打印日期
function printDate(date: string | Date) {
  if (date instanceof Date) {
    console.log(date.getTime())
  } else {
    console.log(date)
  }

  // if (typeof date === "string") {
  //   console.log(date)
  // } else {
  //   console.log(date.getTime())
  // }
}


// 4.in: 判断是否有某一个属性
interface ISwim {
  swim: () => void
}

interface IRun {
  run: () => void
}

function move(animal: ISwim | IRun) {
  if ("swim" in animal) {
    animal.swim()
  } else if ("run" in animal) {
    animal.run()
  }
}

const fish: ISwim = {
  swim: function() {}
}

const dog: IRun = {
  run: function() {}
}

move(fish)
move(dog)
```