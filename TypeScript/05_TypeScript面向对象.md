## 1. ts中类的基本使用

注意 : 要声明成员属性

```typescript
class Person {
  // 成员属性: 声明成员属性
  name: string
  age: number

  constructor(name: string, age: number) {
    this.name = name
    this.age = age
  }

  eating() {
    console.log(this.name + " eating")
  }

  running() {
    console.log(this.name + " running")
  }
}

// 实例对象: instance
const p1 = new Person("why", 18)
const p2 = new Person("kobe", 30)

console.log(p1.name, p2.age)
```



## 2. TS中类的修饰符

### 2.1. TS中类的成员修饰符

* `public`在**任何地方**可见、公有的属性或方法
* `private` 仅在**同一类**中可见、私有的属性或方法
* `protected` 仅在**类自身及子类**中可见、受保护的属性或方法

```typescript
class Person {
  protected name: string
  private age: number

  constructor(name: string, age: number) {
    this.name = name
    this.age = age
  }

  // 方法变成私有方法: 只有在类内部才能访问
  private eating() {
    console.log("吃东西", this.age, this.name)
  }
}

const p = new Person("why", 18)
// console.log(p.name, p.age)
// p.name = "kobe"
// p.eating()

// 子类中是否可以访问
class Student extends Person {
  constructor(name: string, age: number) {
    super(name, age)
  }

  studying() {
    console.log("在学习", this.name)
  }
}

const stu = new Student("why", 18)
```

### 2.2. TS中类的 readonly 修饰符

只读

```typescript
class Person {
  readonly name: string
  age: number

  constructor(name: string, age: number) {
    this.name = name
    this.age = age
  }
}

// 类和实例之间的关系(重要)
const p = new Person("why", 18)
console.log(p.name, p.age)

// p.name = "kobe" 只读属性不能进行写入操作
p.age = 20
```



## 3. TS中类的getters和setters

一般只有私有属性会使用

私有属性我们是不能直接访问的，或者某些属性我们想要监听它的获取(getter)和设置(setter)的过程，这个时候我们可以使用存取器。

```typescript
class Person {
  // 私有属性: 属性前面会使用_
  private _name: string
  private _age: number

  constructor(name: string, age: number) {
    this._name = name
    this._age = age
  }

  running() {
    console.log("running:", this._name)
  }

  // setter/getter: 对属性的访问进行拦截操作
  set name(newValue: string) {
    this._name = newValue
  }

  get name() {
    return this._name
  }


  set age(newValue: number) {
    if (newValue >= 0 && newValue < 200) {
      this._age = newValue
    }
  }

  get age() {
    return this._age
  }
}

const p = new Person("why", 100)
p.name = "kobe"
console.log(p.name)

p.age = -10
console.log(p.age)
```

## 4. TS中类的参数属性使用(语法糖)

参数前面加成员修饰符 (比如 `public`)

你可以通过在构造函数参数前添加一个可见性修饰符 `public private protected` 或者 `readonly` 来创建参数属性，最后这些类属性字段也会得到这些修饰符；

```typescript
class Person {
  // 语法糖
  constructor(public name: string, private _age: number, readonly height: number) {
  }

  running() {
    console.log(this._age, "eating")
  }
}

const p = new Person("why", 18, 1.88)
console.log(p.name, p.height)

// p.height = 1.98  只读 错误
```

## 5. TS中抽象类和抽象方法

* 抽象类中的方法只有声明没有实现体
* 抽象方法必须出现在抽象类中, 类前面也需要加`abstract`
* 抽象类不能被实例化

```typescript
abstract class Shape {
  // getArea方法只有声明没有实现体
  // 实现让子类自己实现
  // 可以将getArea方法定义为抽象方法: 在方法的前面加abstract
  // 抽象方法必须出现在抽象类中, 类前面也需要加abstract
  abstract getArea()
}


class Rectangle extends Shape {
  constructor(public width: number, public height: number) {
    super()
  }

  getArea() {
    return this.width * this.height
  }
}

class Circle extends Shape {
  constructor(public radius: number) {
    super()
  }

  getArea() {
    return this.radius ** 2 * Math.PI
  }
}

class Triangle extends Shape {
  getArea() {
    return 100
  }
}


// 通用的函数 (多态)
function calcArea(shape: Shape) {
  return shape.getArea()
}

calcArea(new Rectangle(10, 20))
calcArea(new Circle(5))
calcArea(new Triangle())

// 在Java中会报错: 不允许
// 鸭子类型(后面会将)
calcArea({ getArea: function() {} })
```



## 6. TS中类型检测-鸭子类型

* `TypeScript`对于类型检测的时候使用的鸭子类型
* 鸭子类型: 如果一只鸟, 走起来像鸭子, 游起来像鸭子, 看起来像鸭子, 那么你可以认为它就是一只鸭子
* 鸭子类型, **只关心属性和行为, 不关心你具体是不是对应的类型**

```typescript
// TypeScript对于类型检测的时候使用的鸭子类型
// 鸭子类型: 如果一只鸟, 走起来像鸭子, 游起来像鸭子, 看起来像鸭子, 那么你可以认为它就是一只鸭子
// 鸭子类型, 只关心属性和行为, 不关心你具体是不是对应的类型

class Person {
  constructor(public name: string, public age: number) {}

  running() {}
}

class Dog {
  constructor(public name: string, public age: number) {}
  running() {}
}

function printPerson(p: Person) {
  console.log(p.name, p.age)
}

printPerson(new Person("why", 18))
// printPerson("abc")
printPerson({name: "kobe", age: 30, running: function() {}})
printPerson(new Dog("旺财", 3))

const person: Person = new Dog("果汁", 5) // 不报错
```

## 7. TS中的类具有类型特性

类的作用 : 

* 可以创建类对应的实例对象
* 类本身可以作为这个实例的类型
* 类也可以当中有一个构造签名的函数

```typescript
class Person {}

/**
 * 类的作用:
 *  1.可以创建类对应的实例对象
 *  2.类本身可以作为这个实例的类型
 *  3.类也可以当中有一个构造签名的函数
 */

const name: string = "aaa"
const p: Person = new Person()
function printPerson(p: Person) {}

function factory(ctor: new () => void) {}
factory(Person)
```

## 8. TS对象类型的属性修饰符

```typescript
// 定义对象类型
type IPerson = {
  // 属性?: 可选的属性
  name?: string
  // readonly: 只读的属性
  readonly age: number
}

interface IKun {
  name?: string
  readonly slogan: string
}

const p: IPerson = {
  name: "why",
  age: 18
}

// p.age = 30
```



## 9. TS中接口的特性



### 9.1. 接口的继承

接口和类一样是可以进行继承的，也是使用extends关键字，接口是支持多继承的（类不支持多继承）

**自定义一个接口, 同时你希望自定义接口拥有第三方某一个类型中所有的属性**

```typescript
interface IPerson {
  name: string
  age: number
}

// 可以从其他的接口中继承过来属性
// 1.减少了相同代码的重复编写
// 2.如果使用第三库, 给我们定义了一些属性
//  > 自定义一个接口, 同时你希望自定义接口拥有第三方某一个类型中所有的属性
//  > 可以使用继承来完成
interface IKun extends IPerson {
  slogan: string
}

const ikun: IKun = {
  name: "why",
  age: 18,
  slogan: "你干嘛, 哎呦"
}
```



### 9.2. 接口被类实现

多个接口被类实现

```typescript
interface IKun {
  name: string
  age: number
  slogan: string

  playBasketball: () => void
}

interface IRun {
  running: () => void
}


// 作用: 多个接口被类实现
class Person implements IKun, IRun {
  name: string
  age: number
  slogan: string

  playBasketball() {}
  running() {}
}

const ikun2 = new Person()
console.log(ikun2.name, ikun2.age, ikun2.slogan)
ikun2.playBasketball()
ikun2.running()
```

## 10. TS中严格字面量赋值检测

解释现象

第一次创建的对象字面量, 称之为`fresh`(新鲜的)

**对于新鲜的字面量, 会进行严格的类型检测. 必须完全满足类型的要求(不能有多余的属性)**

**当类型断言或对象字面量的类型扩大时，新鲜度会消失。**

```typescript
interface IPerson {
  name: string
  age: number
}


// 1.奇怪的现象一: 
// 定义info, 类型是IPerson类型
const obj = {
  name: "why",
  age: 18,

  // 多了一个height属性
  height: 1.88
}
const info: IPerson = obj // 这里不报错


// 2.奇怪的现象二:
function printPerson(person: IPerson) {
}

const kobe = { name: "kobe", age: 30, height: 1.98 }
printPerson(kobe)


// 解释现象
// 第一次创建的对象字面量, 称之为fresh(新鲜的)
// 对于新鲜的字面量, 会进行严格的类型检测. 必须完全满足类型的要求(不能有多余的属性)
```

## 11. TS对象类型的索引签名

 有的时候，你不能提前知道一个类型里的所有属性的名字，但是你知道这些值的特征；

 这种情况，你就可以用一个索引签名 (index signature) 来描述可能的值的类型；

### 11.1. 基本使用

```typescript
// 索引签名的案例
// 可以通过类型为number的索引获取类型为string的值, 同时满足要求有length属性, 值为number
interface ICollection {
  [index: number]: string  // 这里值的类型必须只能是一种, 要么number要么string
  length: number
}

function printCollection(collection: ICollection) {
  for (let i = 0; i < collection.length; i++) {
    const item = collection[i]
    console.log(item.length)
  }
}

const array = ["abc", "cba", "nba"]  // 比如这里就可以array[0]获取
const tuple: [string, string] = ["why", "广州"] // 同上
printCollection(array)
printCollection(tuple)
```



### 11.2. 类型问题

注意看注释

```typescript
interface IIndexType {
  // 返回值类型的目的是告知通过索引去获取到的值是什么类型
  // [index: number]: string
  // [index: string]: string
     [index: string]: any
}

// 索引签名: [index: number]: string  不报错
// 通过数字类型访问索引时, 最终都是转化成string类型访问
const names: IIndexType = ["abc", "cba", "nba"]

// 索引签名: [index: string]: any: 不报错
// 1.索引要求必须是字符串类型 names[0] => names["0"]
const names: IIndexType = ["abc", "cba", "nba"]

// 索引签名: [index: string]: string: 会报错 : 
// 因为返回的可能是一个函数, 比如通过自带(forEach)返回一个函数
// 严格字面量赋值检测: ["abc", "cba", "nba"] => Array实例 => names[0] names.forEach
const names: IIndexType = ["abc", "cba", "nba"]

// names["forEach"] => function  
// 这里返回一个function就不符合[index: string]: string了, 就其原因还是你有其它的属性

// names["map/filter"] => function
```



### 11.3. 两个签名

**数字类型索引的类型, 必须是字符串类型索引的类型的子类型**

如果索引签名中有定义其他属性, 其他属性返回的类型, 必须符合string类型返回的属性

因为数字类型最终会转为字符串类型 , 所以我们要求数字类型是比字符串类型更具体的类型



```typescript
interface IIndexType {
  // 两个索引类型的写法
  [index: number]: string
  [key: string]: any

  // 要求一:下面的写法不允许: 数字类型索引的类型, 必须是字符串类型索引的类型的 子类型
  // 结论: 数字类型必须是比如字符串类型更加确定的类型(需要是字符串类型的子类型)
  // 原因: 所有的数字类型都是会转成字符串类型去对象中获取内容

  // 数字0: string
  // 数字"0": number|string

  // 要求二: 如果索引签名中有定义其他属性, 其他属性返回的类型, 必须符合string类型返回的属性
  // [index: number]: string
  // [key: string]: number|string

  // aaa: string
  // bbb: boolean 错误的类型
}

const names: IIndexType = ["abc", "cba", "nba"]
const item1 = names[0]
const forEachFn = names["forEach"]
```





## 12. TS中的枚举类型(了解)

### 12.1. 基本使用

```typescript
// 定义枚举类型
enum Direction {
  LEFT,
  RIGHT
}

const d1: Direction = Direction.LEFT

function turnDirection(direction: Direction) {
  switch(direction) {
    case Direction.LEFT:
      console.log("角色向左移动一个格子")
      break
    case Direction.RIGHT:
      console.log("角色向右移动一个格子")
      break
  }
}

// 监听键盘的点击
turnDirection(Direction.LEFT)
```

### 12.2. 设置值

```typescript
// 定义枚举类型
// enum Direction {
//   LEFT = 0,
//   RIGHT = 1
// }

// enum Direction {
//   LEFT = 100,
//   RIGHT  就是 101 了
// }

enum Direction {
  LEFT = "LEFT",
  RIGHT = "RIGHT"
}

// 方便后续进行位运算
enum Operation {
  Read = 1 << 0,
  Write = 1 << 1,
  foo = 1 << 2
}

const d1: Direction = Direction.LEFT
```