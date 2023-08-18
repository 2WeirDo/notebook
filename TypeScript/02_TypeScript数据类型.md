## 1. js中的类型

### 1.1. Array

在真实的开发中, 数组一般存**放相同的类型**, 不要存放不同的类型

```typescript
// 明确的指定<数组>的类型注解: 两种写法
// 1. string[]: 数组类型, 并且数组中存放的字符串类型
// 2. Array<string>: 数组类型, 并且数组中存放的是字符串类型

// 注意事项: 在真实的开发中, 数组一般存放相同的类型, 不要存放不同的类型
let names: string[] = ["abc", "cba", "nba"]
names.push("aaa")
// names.push(123)

let nums: Array<number> = [123, 321, 111]
```

### 1.2. Object

```typescript
let info = {
  name: "why",
  age: 18,
  height: 1.88
}

console.log(info.name)
console.log(info.age)
```



## 2. 函数的类型

### 2.1. 参数

需要明确的指定参数的类型

```typescript
// 在定义一个TypeScript中的函数时, 都要明确的指定参数的类型
function sum(num1: number, num2: number) {
  return num1 + num2
}

const res = sum(123, 321)
// sum("abc", "cba")
// sum({}, {})
```

### 2.2. 返回值

返回值类型可以明确的指定, 也可以自动进行类型推导

```typescript
// 在定义一个TypeScript中的函数时
// 返回值类型可以明确的指定, 也可以自动进行类型推导
function sum(num1: number, num2: number): number {
  return num1 + num2
}

const res = sum(123, 321)
```

### 2.3. 练习

```typescript
// 定义对象类型
type LyricType = {
  time: number
  text: string
}

// 歌词解析工具
function parseLyric(lyric: string): LyricType[] {
  const lyrics: LyricType[] = []
  lyrics.push({ time: 1111, text: "天空想要下雨" })
  return lyrics
}

const lyricInfos = parseLyric("fdafdafdafa")
for (const item of lyricInfos) {
  console.log(item.time, item.text)
}
```

### 2.4. 匿名的参数类型

匿名函数是否需要添加类型注解呢? 

**最好不要添加类型注解**

 这是因为TypeScript会根据forEach函数的类型以及数组的类型推断出item的类型；

 这个过程称之为**上下文类型**（contextual typing），因为函数执行的上下文可以帮助确定参数和返回值的类型；

```typescript
const names: string[] = ["abc", "cba", "nba"]

// 匿名函数是否需要添加类型注解呢? 最好不要添加类型注解
names.forEach(function(item, index, arr) {
  console.log(item, index, arr)
})
```

### 2.5. 对象类型的使用

```typescript
type PointType = {
  x: number
  y: number
  z?: number
  // 对象类型也可以指定哪些属性是可选的，可以在属性的后面添加一个?
}
function printCoordinate(point: PointType) {
  console.log("x坐标:", point.x)
  console.log("y坐标:", point.y)
}

// printCoordinate(123)
printCoordinate({ x: 20, y: 30 })
```

通过 type 定义一个对象类型



## 3. ts中的类型

### 3.1. any

`any`类型就表示**不限制标识符的任意类型**, 并且可以在该标识符上面进行任意的操作

我们确实无法确定一个变量的类型，并且可能它会发生一些变化，这个时候我们可以使用any类型

```typescript
// any类型就表示不限制标识符的任意类型, 并且可以在该标识符上面进行任意的操作
let id: any = "aaaa"

id = "bbbb"
id = 123
console.log(id.length)

id = { name: "why", level: 99 }

// 定义数组
const infos: any[] = ["abc", 123, {}, []]
```

### 3.2. unknown

要求必须进行**类型的校验(缩小), 才能根据缩小之后的类型, 进行对应的操作**

**unknown是TypeScript中比较特殊的一种类型，它用于描述类型不确定的变量。**

**和any类型有点类似，但是unknown类型的值上做任何事情都是不合法的**

```typescript
let foo: unknown = "aaa"
foo = 123
// console.log(foo.length);

// unknown类型默认情况下在上面进行任意的操作都是非法的
// 要求必须进行类型的校验(缩小), 才能根据缩小之后的类型, 进行对应的操作
if (typeof foo === "string") { // 类型缩小
  console.log(foo.length, foo.split(" "))
}
```

### 3.3. void

void通常用来指定一个函数是没有返回值的，那么它的返回值就是void类型：

 这个函数我们没有写任何类型，那么它默认返回值的类型就是void的，我们也可以显示的来指定返回值是void

我们可以将undefined赋值给void类型，也就是函数可以返回undefined

当基于上下文的类型推导（Contextual Typing）推导出返回类型为 void 的时候，并不会强制函数一定不能返回内容。

```typescript
// 1.在TS中如果一个函数没有任何的返回值, 那么返回值的类型就是void类型
// 2.如果返回值是void类型, 那么我们也可以返回undefined(TS编译器允许这样做而已)
function sum(num1: number, num2: number): void {
  console.log(num1 + num2)
  // return 123 错误的做法
}

// parseLyric => 函数/对象
type FooType = () => void
const foo: FooType = () => {}



// 举个例子:(涉及函数的类型问题, 后续还会详细讲解)
// 1.定义要求传入的函数的类型
type ExecFnType = (...args: any[]) => void

// 2.定义一个函数, 并且接收的参数也是一个函数, 而且这个函数的类型必须是ExecFnType
function delayExecFn(fn: ExecFnType) {
  setTimeout(() => {
    fn("why", 18)
  }, 1000);
}

// 3.执行上面函数, 并且传入一个匿名函数
delayExecFn((name, age) => {
  console.log(name, age)
})

// 4. 基于上下文类型推导的函数中的返回值如果是void类型, 并且不强制要求不能返回任何的东西
const names = ["abc", "cba", "nba"]
names.forEach((item: string, index: number, arr: string[]) => {
  console.log(item)
  return 123  // 这里是不会报错的
})
```

### 3.4. never (了解)

实际开发中只有进行类型推导时, 可能会自动推导出来是`never`类型, 但是很少使用它

封装框架/工具库的时候可以使用一下never

```typescript
// 其他时候在扩展工具的时候, 对于一些没有处理的case, 可以直接报错
function handleMessage(message: string | number | boolean) {
  switch (typeof message) {
    case "string":
      console.log(message.length)
      break
    case "number":
      console.log(message)
      break
    // case "boolean":
    //   console.log(Number(message))
    //   break
    // 当我们传入了boolean类型但是没有对其进行操作时, 我们会进入default, 
    // 而我们的never不能等于传递的boolean类型, 因此会报错
    default:
      const check: never = message
  }
}

handleMessage("aaaa")
handleMessage(1234)

// 另外同事调用这个函数
handleMessage(true)  // 若注释了上面case "boolean"的代码, 此处就不能进行了
```



### 3.5. tuple

元组数据结构中可**以存放不同的数据类型**, 取出来的item也是**有明确的类型**

**在函数中使用元组类型是最多的**

```typescript
// 保存我的个人信息: why 18 1.88
// 1.使用数组类型
// 不合适: 数组中最好存放相同的数据类型, 获取值之后不能明确的知道对应的数据类型
const info1: any[] = ["why", 18, 1.88]
const value = info1[2]
console.log()


// 2.使用对象类型(最多)
const info2 = {
  name: "why",
  age: 18,
  height: 1.88
}

// 3.使用元组类型
// 元组数据结构中可以存放不同的数据类型, 取出来的item也是有明确的类型
const info3: [string, number, number] = ["why", 18, 1.88]
const value2 = info3[2]


// 这里是一个例子
// 在函数中使用元组类型是最多的(函数的返回值)
function useState(initialState: number): [number, (newValue: number) => void] {
  let stateValue = initialState
  function setValue(newValue: number) {
    stateValue = newValue
  }

  return [stateValue, setValue]
}

const [count, setCount] = useState(10)
console.log(count)
setCount(100)
```