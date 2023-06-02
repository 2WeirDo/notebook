## 1. `storage`

### 1.1. `storage`的基本操作

注意只能在html中进行调试 

 node中  ![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1683206107564-9f31f5ca-51ca-4885-9375-4f0e77646518.png)

```javascript
// 1.token的操作
let token = localStorage.getItem("token")
if (!token) {
  console.log("从服务器获取token")
  token = "coderwhytokeninfo"
  localStorage.setItem("token", token)
}

// 2.username/password的操作
let username = localStorage.getItem("username")
let password = localStorage.getItem("password")
if (!username || !password) {
  console.log("让用户输入账号和密码")
  username = "coderwhy"
  password = "123456"
  // 将token/username/password保存到storage
  localStorage.setItem("username", username)
  localStorage.setItem("password", password)
}

// 3.后续的操作
console.log(token)
console.log(token.length)
console.log(token + " 哈哈哈")
console.log(username);
console.log(password);
```

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1683206038401-6ccabdde-7494-4c1e-b343-9d384e889774.png)



### 1.2. `localStorage`和`sessionStorage`区别

```html
<a href="./static/about.html">关于</a>
```

以下为`js`代码

```javascript
// 1.验证一: 关闭网页
// 1.1.localStorage的存储保持
localStorage.setItem("name", "localStorage")

// 1.2.sessionStorage的存储会消失
sessionStorage.setItem("name", "sessionStorage")
// 2.验证二: 打开新的网页(点击a标签, 在当前网页操作, 不跳转到新的网页)
<a href="./static/about.html">关于</a>
localStorage.setItem("info", "local")
sessionStorage.setItem("info", "session")

// 这个时候两个storage都在
// 3.验证三: 打开新的页面, 并且是在新的标签中打开 (点击a标签)

 <a href="./static/about.html"  target="_blank">关于</a>
localStorage.setItem("infoTab", "local");
sessionStorage.setItem("infoTab", "session");

// 这时 sessionstorage 已经不在
```



### 1.3. `Storage`的其它方法

```
key` / `length` / `removeitem` / `clear
const ACCESS_TOKEN = "token"

console.log(localStorage.length)
// 常见的方法:
localStorage.setItem(ACCESS_TOKEN, "whytoken")
console.log(localStorage.getItem(ACCESS_TOKEN))

// 其他方法
console.log(localStorage.key(0))
console.log(localStorage.key(1))
console.log(localStorage.removeItem("infoTab"))
localStorage.clear()
```



### 1.4. `Storage`工具封装

使其能够存储对象类型, 也就是说自己封装能够更加灵活

```javascript
class Cache {
  constructor(isLocal = true) {
    this.storage = isLocal ? localStorage: sessionStorage
  }

  setCache(key, value) {
    if (!value) {
      throw new Error("value error: value必须有值!")
    }

    if (value) {
      this.storage.setItem(key, JSON.stringify(value))  // 
    }
  }

  getCache(key) {
    const result = this.storage.getItem(key)
    if (result) {
      return JSON.parse(result)
    }
  }

  removeCache(key) {
    this.storage.removeItem(key)
  }

  clear() {
    this.storage.clear()
  }
}
const localCache = new Cache()
const sessionCache = new Cache(false)

localCache.setCache("sno", 111)

// storage本身是不能直接存储对象类型的
const userInfo = {
  name: "why",
  nickname: "coderwhy",
  level: 100,
  imgURL: "http://github.com/coderwhy.png"
}

// localStorage.setItem("userInfo", JSON.stringify(userInfo))
// console.log(JSON.parse(localStorage.getItem("userInfo")))

sessionCache.setCache("userInfo", userInfo)
console.log(sessionCache.getCache("userInfo"))
```



## 2. RegExp (正则)

### 2.1. 正则表达式的创建

```javascript
// 创建正则
// 1> 匹配的规则pattern
// 2> 匹配的修饰符flags
const re1 = new RegExp("abc", "ig")
const re2 = /abc/ig // 我是注释
```



### 2.2. 正则表达式的演练

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1683376279871-932e1f6a-8584-468c-908b-91ca5354293a.png)



### 2.3. 修饰符`flag`的使用

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1683376357057-3f377c9c-7434-4ca9-8ea7-09348a9be4ac.png)

### 2.4. 使用字符串的方法



```
replaceAll` / `replace` /  `match` / `matchAll`  / `split` / `search
// 创建正则
const re1 = /abc/ig
const message = "fdabc123 faBC323 dfABC222 A2324aaBc"


// 需求: 将所有的abc(忽略大小写)换成cba
const re1 = /abc/ig
const message = "fdabc123 faBC323 dfABC222 A2324aaBc"
const newMessage = message.replaceAll(/abc/ig, "cba")
//  或者  const newMessage = message.replaceAll("abc", "cba")
console.log(newMessage)
// fdcba123 fcba323 dfcba222 A2324acba


// 需求: 将字符串中数字全部删除
const re1 = /abc/ig
const message = "fdabc123 faBC323 dfABC222 A2324aaBc"
const newMessage2 = message.replaceAll(/\d+/ig, "")
console.log(newMessage2)
// fdabc faBC dfABC AaaBc



// 2.1. match方法: 🔥
const re1 = /abc/ig
const message = "fdabc123 faBC323 dfABC222 A2324aaBc"
const result2 = message.match(re1)
console.log(result2)
// [ 'abc', 'aBC', 'ABC', 'aBc' ]




// 2.2. matchAll方法

// 注意: matchAll传入的正则修饰符必须加g
const re1 = /abc/ig
const message = "fdabc123 faBC323 dfABC222 A2324aaBc"
const result3 = message.matchAll(re1)
// console.log(result3.next())
// console.log(result3.next())
// console.log(result3.next())
// console.log(result3.next())
for (const item of result3) {
  console.log(item)
}
// [
//   'abc',
//   index: 2,
//   input: 'fdabc123 faBC323 dfABC222 A2324aaBc',
//   groups: undefined
// ]
// [
//   'aBC',
//   index: 10,
//   input: 'fdabc123 faBC323 dfABC222 A2324aaBc',
//   groups: undefined
// ]
// [
//   'ABC',
//   index: 19,
//   input: 'fdabc123 faBC323 dfABC222 A2324aaBc',
//   groups: undefined
// ]
// [
//   'aBc',
//   index: 32,
//   input: 'fdabc123 faBC323 dfABC222 A2324aaBc',
//   groups: undefined
// ]



// 2.3. replace/replaceAll方法
// 在上面



// 2.4. split方法
const re1 = /abc/ig
const message = "fdabc123 faBC323 dfABC222 A2324aaBc"
const result4 = message.split(re1)
console.log(result4)
// [ 'fd', '123 f', '323 df', '222 A2324a', '' ]



// 2.5. search方法
const re1 = /abc/ig
const message = "fdabc123 faBC323 dfABC222 A2324aaBc"
const result5 = message.search(re1)
console.log(result5)
// 2
```







### 2.5. 使用正则对象上的实例方法



`test`方法,  `exec`方法

```javascript
// 创建正则
const re1 = /abc/ig
const message = "fdabc123 faBC323 dfABC222 A2324aaBc"



// 1.1.test方法: 检测某一个字符串是否符合正则的规则  🔥
if (re1.test(message)) {
  console.log("message符合规则")
} else {
  console.log("message不符合规则")
}

// message符合规则



// 1.2.exec方法: 使用正则执行一个字符串
const res1 = re1.exec(message)
console.log(res1)

// [
//   'abc',
//   index: 2,
//   input: 'fdabc123 faBC323 dfABC222 A2324aaBc',
//   groups: undefined
// ]


// 案例: 让用户输入的账号必须是aaaaa以上
const inputEl = document.querySelector("input")
const tipEl = document.querySelector(".tip")

inputEl.oninput = function() {
  const value = this.value
  if (/^a{5,8}$/.test(value)) {
    tipEl.textContent = "输入的内容符合规则要求"
  } else {
    tipEl.textContent =  "输入的内容不符合规则要求, 请重新输入"
  }
}
```





### 2.6. 正则规则



#### 2.6.1. 字符类 `\d`

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1683376468394-021a3944-cba8-4133-9ef7-425193196868.png)

```javascript
const message = "fdaa4 22242asfasdf2242"

const re = /\d+/ig

// \d -> 所有的数字 0~9
console.log(message.match(re))

// [ '4', '22242', '2242' ]
```



#### 2.6.2. 锚点 `^` `$`

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1683376538856-5c904fe4-f7b3-413f-93a4-7f4b788cb4ce.png)

```javascript
const message = "My name is WHY."

// 字符串方法  确定一个字符串是否以另一个字符串开头或者结尾。这个方法区分大小写。
if (message.startsWith("my")) {  // false
  console.log("以my开头")
}
if (message.endsWith("why")) {  // false
  console.log("以why结尾")
}

// 正则: 锚点
if (/^my/i.test(message)) {   // true
  console.log("以my开头") 
}

if (/why\.$/i.test(message)) {  // true
   console.log("以why结尾")
}


const re = /^coder$/
const info = "codaaaer"
console.log(re.test(info))    // false
```



#### 2.6.3. 词边界 `\b`

```javascript
const message = "My name! is WHY."

// 需求: name, name必须是一个单独的词
if (/\bname\b/i.test(message)) {
  console.log("有name, name有边界")
}
// 有name, name有边界


// 词边界的应用
const infos = "now time is 11:56, 12:00 eat food, number is 123:456"
const timeRe = /\b\d\d:\d\d\b/ig
console.log(infos.match(timeRe))

// [ '11:56', '12:00' ]
```

有三种不同的位置可作为词边界：

1. 在字符串开头，如果第一个字符是单词字符 \w。
2. 在字符串中的两个字符之间，其中一个是单词字符 \w，另一个不是。
3. 在字符串末尾，如果最后一个字符是单词字符 \w。



#### 2.6.4. 转义 `\`

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1683376691193-7d70dd41-c9b5-415d-b4c4-50837a9c9c61.png)

```javascript
// 定义正则: 对.转义
const re = /\./ig

// 特殊: 对/转义
const re2 = /\//

// 案例 : 匹配所有以.js或者jsx结尾的文件名
// x?: x是0个或者1个

const fileNames = ["abc.html", "Home.jsx", "index.html", "index.js", "util.js", "format.js"]
const jsfileRe = /\.jsx?$/
// 1.for循环做法
const newFileNames = []
for (const filename of fileNames) {
  if (jsfileRe.test(filename)) {
    newFileNames.push(filename)
  }
}
console.log(newFileNames)
// [ 'Home.jsx', 'index.js', 'util.js', 'format.js' ]

// 2.`filter`高阶函数
const newFileNames = fileNames.filter(filename => jsfileRe.test(filename))
console.log(newFileNames)
```



#### 2.6.5. 集合  `[]`

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1683376722139-66387b3d-e371-4a00-8d37-cba08b3476b4.png)

```javascript
// 手机号的规则: 1[3456789]033334444

const phoneStarts = ["132", "130", "110", "120", "133", "155"]
const phoneStartRe = /^1[3456789]\d/
const filterPhone = phoneStarts.filter(phone => phoneStartRe.test(phone))
console.log(filterPhone)
// [ '132', '130', '133', '155' ]


const phoneNum = "133888855555"
const phoneRe = /^1[3-9]\d{9}$/
console.log(phoneRe.test(phoneNum))
// fasle

// 了解: 排除范围
// \d -> [0-9]
// \D -> [^0-9]
```



#### 2.6.6. 量词 `+` `?`  `*`

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1683376782868-95ff9b96-4d55-44ea-88c0-cf6dfee8fb59.png)

```javascript
// 1.量词的使用
const re = /a{3,5}/ig
const message = "fdaaa2fdaaaaaasf42532fdaagjkljlaaaaf"

const results = message.match(re)
console.log(results)
  // [ 'aaa', 'aaaaa', 'aaaa' ]


  // 2.常见的符号: +/?/*
  +: {1,}
  ?: {0,1}
  *: {0,}


// 3.案例: 字符串的html元素, 匹配出来里面所有的标签
const htmlElement = "<div><span>哈哈哈</span><h2>我是标题</h2></div>"
const tagRe = /<\/?[a-z][a-z0-9]*>/ig
const results2 = htmlElement.match(tagRe)
console.log(results2)
// [ '<div>', '<span>', '</span>', '<h2>', '</h2>', '</div>' ]
```



#### 2.6.7. 贪婪惰性 (量词后添加`?`)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1683376818777-05f3d6f9-bec8-452b-8e10-05c3aab013c8.png)

```javascript
 // 1.贪婪模式/惰性模式
 const message = "我最喜欢的两本书: 《黄金时代》和《沉默的大多数》、《一只特立独行的猪》"


 // 默认.+采用贪婪模式
 const nameRe = /《.+》/ig
 const result1 = message.match(nameRe)
 console.log(result1)
// [ '《黄金时代》和《沉默的大多数》、《一只特立独行的猪》' ]



 // 使用惰性模式
 const nameRe = /《.+?》/ig
 const result1 = message.match(nameRe)
 console.log(result1)
// [ '《黄金时代》', '《沉默的大多数》', '《一只特立独行的猪》' ]
```



#### 2.6.8. 捕获组 `()`

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1683376991776-64d14f7c-3ef7-4fd4-bc4f-236f5991f6bb.png)

`.`代表匹配除换行符 `\n` 之外的任何**单字符**。要匹配`.` ，请使用 `\.` 。

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1683377001229-ac4ed0e5-e0d8-4ec9-b296-7be0650d72e9.png)

```javascript
// 1.捕获组
const message = "我最喜欢的两本书: 《黄金时代》和《沉默的大多数》、《一只特立独行的猪》"

// 使用惰性模式  (了解即可)
const nameRe = /(?:《)(?<why>.+?)(?:》)/ig
const iterator = message.matchAll(nameRe)
for (const item of iterator) {
  console.log(item)
}
// [
//   '《黄金时代》',
//   '黄金时代',
//   index: 10,
//   input: '我最喜欢的两本书: 《黄金时代》和《沉默的大多数》、《一只特立独行的猪》',
//   groups: [Object: null prototype] { why: '黄金时代' }
// ]
// [
//   '《沉默的大多数》',
//   '沉默的大多数',
//   index: 17,
//   input: '我最喜欢的两本书: 《黄金时代》和《沉默的大多数》、《一只特立独行的猪》',
//   groups: [Object: null prototype] { why: '沉默的大多数' }
// ]
// [
//   '《一只特立独行的猪》',
//   '一只特立独行的猪',
//   index: 26,
//   input: '我最喜欢的两本书: 《黄金时代》和《沉默的大多数》、《一只特立独行的猪》',
//   groups: [Object: null prototype] { why: '一只特立独行的猪' }
// ]



// 2.将捕获组作为整体
const info = "dfabcabcfabcdfdabcabcabcljll;jk;j"
const abcRe = /(abc){2,}/ig
console.log(info.match(abcRe))
// [ 'abcabc', 'abcabcabc' ]
```





#### 2.6.9. 或 `|`

```javascript
// 1.将捕获组作为整体
const info = "dfabcabcfabcdfdabcabcabcljcbacballnbanba;jk;j"
const abcRe = /(abc|cba|nba){2,}/ig
console.log(info.match(abcRe))
// [ 'abcabc', 'abcabcabc', 'cbacba', 'nbanba' ]
```





### 2.7. 正则练习



#### 2.7.1. 歌词解析



```javascript
/*
      [00:00.000] 作词 : 许嵩 -> { time: 0, content: "作词 : 许嵩" }
      [00:01.000] 作曲 : 许嵩 -> { time: 1000, content: "作曲 : 许嵩" }
      [00:02.000] 编曲 : 许嵩
      [00:22.240]天空好想下雨
    */
const lyricString = "[00:00.000] 作词 : 许嵩\n[00:01.000] 作曲 : 许嵩\n[00:02.000] 编曲 : 许嵩\n[00:22.240]天空好想下雨\n[00:24.380]我好想住你隔壁\n[00:26.810]傻站在你家楼下\n[00:29.500]抬起头数乌云\n[00:31.160]如果场景里出现一架钢琴\n[00:33.640]我会唱歌给你听\n[00:35.900]哪怕好多盆水往下淋\n[00:41.060]夏天快要过去\n[00:43.340]请你少买冰淇淋\n[00:45.680]天凉就别穿短裙\n[00:47.830]别再那么淘气\n[00:50.060]如果有时不那么开心\n[00:52.470]我愿意将格洛米借给你\n[00:55.020]你其实明白我心意\n[00:58.290]为你唱这首歌没有什么风格\n[01:02.976]它仅仅代表着我想给你快乐\n[01:07.840]为你解冻冰河为你做一只扑火的飞蛾\n[01:12.998]没有什么事情是不值得\n[01:17.489]为你唱这首歌没有什么风格\n[01:21.998]它仅仅代表着我希望你快乐\n[01:26.688]为你辗转反侧为你放弃世界有何不可\n[01:32.328]夏末秋凉里带一点温热有换季的颜色\n[01:41.040]\n[01:57.908]天空好想下雨\n[01:59.378]我好想住你隔壁\n[02:02.296]傻站在你家楼下\n[02:03.846]抬起头数乌云\n[02:06.183]如果场景里出现一架钢琴\n[02:08.875]我会唱歌给你听\n[02:10.974]哪怕好多盆水往下淋\n[02:15.325]夏天快要过去\n[02:18.345]请你少买冰淇淋\n[02:21.484]天凉就别穿短裙\n[02:22.914]别再那么淘气\n[02:25.185]如果有时不那么开心\n[02:27.625]我愿意将格洛米借给你\n[02:30.015]你其实明白我心意\n[02:33.327]为你唱这首歌没有什么风格\n[02:37.976]它仅仅代表着我想给你快乐\n[02:42.835]为你解冻冰河为你做一只扑火的飞蛾\n[02:48.406]没有什么事情是不值得\n[02:52.416]为你唱这首歌没有什么风格\n[02:57.077]它仅仅代表着我希望你快乐\n[03:01.993]为你辗转反侧为你放弃世界有何不可\n[03:07.494]夏末秋凉里带一点温热\n[03:11.536]\n[03:20.924]为你解冻冰河为你做一只扑火的飞蛾\n[03:26.615]没有什么事情是不值得\n[03:30.525]为你唱这首歌没有什么风格\n[03:35.196]它仅仅代表着我希望你快乐\n[03:39.946]为你辗转反侧为你放弃世界有何不可\n[03:45.644]夏末秋凉里带一点温热有换季的颜色\n"

// 一. 没有封装

// 1.根据\n切割字符串
const lyricLineStrings = lyricString.split("\n")
// console.log(lyricLineStrings)

// 2.针对每一行歌词进行解析
// [01:22.550]夏末秋凉里带一点温热有换季的颜色
const timeRe = /\[(\d{2}):(\d{2})\.(\d{2,3})\]/i
const lyricInfos = []
for (const lineString of lyricLineStrings) {
  // 1.获取时间
  const result = lineString.match(timeRe)
  if (!result) continue
  const minuteTime = result[1] * 60 * 1000
  const secondTime = result[2] * 1000
  const mSecondTime = result[3].length === 3? result[3]*1: result[3]*10
  const time = minuteTime + secondTime + mSecondTime

  // 2.获取内容
  const content = lineString.replace(timeRe, "").trim()

  // 3.将对象放到数组中
  lyricInfos.push({ time, content })
}
console.log(lyricInfos)




// 二.封装工具: 解析歌词

function parseLyric(lyricString) {
  // 1.根据\n切割字符串
  const lyricLineStrings = lyricString.split("\n")
  // console.log(lyricLineStrings)

  // 2.针对每一行歌词进行解析
  // [01:22.550]夏末秋凉里带一点温热有换季的颜色
  const timeRe = /\[(\d{2}):(\d{2})\.(\d{2,3})\]/i
  const lyricInfos = []
  for (const lineString of lyricLineStrings) {
    // 1.获取时间
    const result = lineString.match(timeRe)
    if (!result) continue
    const minuteTime = result[1] * 60 * 1000
    const secondTime = result[2] * 1000
    const mSecondTime = result[3].length === 3? result[3]*1: result[3]*10
    const time = minuteTime + secondTime + mSecondTime

    // 2.获取内容
    const content = lineString.replace(timeRe, "").trim()

    // 3.将对象放到数组中
    lyricInfos.push({ time, content })
  }

  return lyricInfos
}

// const lyricInfos = parseLyric(lyricString)
// console.log(lyricInfos)


// 输出为以下
// [
//   { time: 0, content: '作词 : 许嵩' },
//   { time: 1000, content: '作曲 : 许嵩' },
//   { time: 2000, content: '编曲 : 许嵩' },
//   { time: 22240, content: '天空好想下雨' },
//   { time: 24380, content: '我好想住你隔壁' },
//   { time: 26810, content: '傻站在你家楼下' },
//   { time: 29500, content: '抬起头数乌云' },
//   { time: 31160, content: '如果场景里出现一架钢琴' },
  ...// 手动省略..
// ]
```





#### 2.7.2. 时间格式化

```javascript
  // timestamp: 1659252290626
    // yyyy/MM/dd hh:mm:ss
    // yyyy*MM*dd hh-mm-ss
    function formatTime(timestamp, fmtString) {
        // 1.将时间戳转成Date
        const date = new Date(timestamp)
  
        // // 2.获取到值
        // const year = date.getFullYear()
        // const month = date.getMonth() + 1
        // const day = date.getDate()
        // const hour = date.getHours()
        // const minute = date.getMinutes()
        // const second = date.getSeconds()
  
        // // 3.创建正则
        // const yearRe = /y+/
        // const monthRe = /M+/
  
        // 2.正则和值匹配起来
        const dateO = {
          "y+": date.getFullYear(),
          "M+": date.getMonth() + 1,
          "d+": date.getDate(),
          "h+": date.getHours(),
          "m+": date.getMinutes(),
          "s+": date.getSeconds()
        }
  
        // 3.for循环进行替换
        for (const key in dateO) {
          const keyRe = new RegExp(key)
          if (keyRe.test(fmtString)) {
            const value = (dateO[key] + "").padStart(2, "0")
            fmtString = fmtString.replace(keyRe, value)
          }
        }
  
        return fmtString
      }
  
      // 某一个商品上架时间, 活动的结束时间
      const timeEl = document.querySelector(".time")
      const productJSON = {
        name: "iPhone",
        newPrice: 4999,
        oldPrice: 5999,
        endTime: 1659252301637
      }
      timeEl.textContent = formatTime(productJSON.endTime, "hh:mm:ss yyyy*MM*dd")
  
```