## 1. 泛型

### 1.1. 类型的参数化

```typescript
// 1.理解形参和实例参数化, 但是参数的类型是固定的
function foo(name: string, age: number) {
}
foo("why", 19)
foo("kobe", 30)


// 2.定义函数: 将传入的内容返回
function bar<Type>(arg: Type): Type {
  return arg
}


// 2.1. 完整的写法
// 传入类型
const res1 = bar<number>(123)
const res2 = bar<string>("abc")
const res3 = bar<{name: string}>({ name: "why" })

// 2.2. 省略的写法 
// 因为它可以自动推断类型 -- 很方便
const res4 = bar("aaaaaaaaa")
const res5 = bar(11111111)

// const是字面量类型, let是构造函数类型
```



### 1.2. useState的练习

```typescript
// 元组: useState函数
function useState<Type>(initialState: Type): [Type, (newState: Type) => void] {
  // 这里传入返回值类型是因为它可能类型推断不正确, 当然正确就不用传递了
  let state = initialState
  function setState(newState: Type) {
    state = newState
  }
  return [state, setState]
}

// 初始化count
const [count, setCount] = useState(100)
const [message, setMessage] = useState("Hello World")
// 当类型推断不正确的时候, 就要自己指定类型了
const [banners, setBanners] = useState<any[]>([])
```

### 1.3. 传入多个类型

泛型常用名称

 `T`：Type的缩写，类型

 `K`、V：key和value的缩写，键值对

 `E`：Element的缩写，元素

 `O`：Object的缩写，对象

```typescript
function foo<T, E>(arg1: T, arg2: E) {
}

foo(10, 20)
foo(10, "abc")
foo<string, { name: string }>("abc", { name: "why" })
```



### 1.4. 泛型接口使用

```typescript
interface IKun<Type = string> {
  name: Type
  age: number
  slogan: Type
}

const kunkun: IKun<string> = {
  name: "why",
  age: 18,
  slogan: "哈哈哈"
}

const ikun2: IKun<number> = {
  name: 123,
  age: 20,
  slogan: 666
}

// 不传就的话就等于默认值
const ikun3: IKun = {
  name: "kobe",
  age: 30,
  slogan: "坤坤加油!"
}
```



### 1.5. 泛型类的使用

这里用到了类的参数属性(语法糖)

```typescript
class Point<Type = number> {
  constructor(public x: Type, public y: Type) {
  }
}

const p1 = new Point(10, 20)
console.log(p1.x)
const p2 = new Point("123", "321")
console.log(p2.x)
```



### 1.6. 泛型约束使用

要求 : 

1. 获取传入的内容, 这个内容必须有length属性
2. 能够自己判断出传入的类型

```typescript
interface ILength {
  length: number
}

// 获取传入的内容, 这个内容必须有length属性
// Type相当于是一个变量, 用于记录本次调用的类型, 所以在整个函数的执行周期中, 一直保留着参数的类型
function getInfo<Type extends ILength>(args: Type): Type {
  return args
}

// 我们想要获取的不仅仅是能够自己判断出传入的类型, 
// 还要拥有有length属性的类型, 所以我们需要使用泛型约束extends ILength
const info1 = getInfo("aaaa")
const info2 = getInfo(["aaa", "bbb", "ccc"])
const info3 = getInfo({ length: 100 })
```



### 1.7. 泛型参数使用约束

确保我们不会获取 obj 上不存在的属性；

```typescript
// 传入的key类型, obj当中key的其中之一
interface IKun {
  name: string
  age: number
}

type IKunKeys = keyof IKun // 等于 "name"|"age"

// 这个函数的作用是传入一个对象和一个key 返回这个值
function getObjectProperty<O, K extends keyof O>(obj: O, key: K){
  // K extends keyof O 这一坨在这里相当于 "name"|"age"|"height"
  return obj[key]
}

const info = {
  name: "why",
  age: 18,
  height: 1.88
}

const name = getObjectProperty(info, "name")
// 这样它就能自己判断是什么类型了, 并且传入不存在的参数会报错
const name = getObjectProperty(info, "address") // 这里就会报错, "address"不存在于info对象中
```

使用约束可以让您在编写泛型代码时更精确地定义参数的类型要求，从而提高代码的可读性、可维护性和类型安全性。

## 2. 映射类型

有的时候，一个类型需要基于另外一个类型，但是你又**不想拷贝一份**，这个时候可以考虑使用映射类型

 映射类型建立在索引签名的语法上

### 2.1. 基本使用

注意是用的 `type` 进行定义

```typescript
// TypeScript提供了映射类型: 函数
// 映射类型不能使用interface定义

type MapPerson<Type> = {
  // 索引类型以此进行使用
  [aaa in keyof Type]: Type[aaa] // aaa也就是名字随便取
  
  // 上面那一行就相当于下面这两行
  // name: string
  // age: number 
}


interface IPerson {
  name: string
  age: number
}

type NewPerson = MapPerson<IPerson>
// 这样我们就有了和IPerson一样的接口了 , 当然cv也一样可以, 不过就不能进行其它操作了
```

### 2.2. 修饰符使用

```typescript
type MapPerson<Type> = {
  readonly [Property in keyof Type]?: Type[Property]
  // readonly 只读, ? 可选
}

interface IPerson {
  name: string
  age: number
  height: number
  address: string
}

type p = MapPerson<IPerson>
// 鼠标移动到 p 身上就会发现属性都是只读且可选的了
```

### 2.3. 修饰符符号

通过符号操作可以将原本的接口中的属性进行更改(比如不要只读, 或者不要可选)

```typescript
type MapPerson<Type> = {
  -readonly [Property in keyof Type]-?: Type[Property]
}

interface IPerson {
  name: string
  age?: number
  readonly height: number
  address?: string
}

// 将?可选和readonly只读删除
type IPersonRequired = MapPerson<IPerson>

const p: IPersonRequired = {
  name: "why",
  age: 18,
  height: 1.88,
  address: "广州市"
}
```