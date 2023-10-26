## 1. 认识Axios库

◼ 功能特点:

*  在浏览器中发送 XMLHttpRequests 请求
*  在 node.js 中发送 http请求

相当于合二为一了, 而不用考虑在什么环境用什么请求

*  支持 Promise API
*  拦截请求和响应
*  转换请求和响应数据

## 2. axios发送请求	

### 2.1. axios请求方式

◼ 支持多种请求方式:

*  axios(config)
*  **axios.request(config)**
*  **axios.get(url[, config])**
*  axios.delete(url[, config])
*  axios.head(url[, config])
*  **axios.post(url[, data[, config]])**
*  axios.put(url[, data[, config]])

 axios.patch(url[, data[, config]])

◼ 有时候, 我们可能需求同时发送两个请求

*  使用`axios.all`, 可以放入多个请求的数组.

作用和 `promise.all` 一样 , 原理也是调用的`promise.all`

*  `axios.all([])` 返回的结果是一个数组，使用 `axios.spread` 可将数组 [res1,res2] 展开为 res1, res2



### 2.2. 常见的配置选项

◼ **请求地址**

*  `url`: '/user',

◼ **请求类型**

*  `method`: 'get',

◼ **请根路径**

*  `baseURL`: 'http://www.mt.com/api',

◼ 请求前的数据处理

*  `transformRequest`:[function(data){}],

◼ 请求后的数据处理

*  `transformResponse`: [function(data){}],

◼ 自定义的请求头

*  `headers`:{'x-Requested-With':'XMLHttpRequest'},

◼ URL查询对象

*  `params`:{ id: 12 },

◼ 查询对象序列化函数

*  `paramsSerializer`: function(params){ }

◼ **request body**

*  `data`: { key: 'aa'},

◼ **超时设置**

*  `timeout`: 1000,



### 2.3. 常见请求演练

发送`request`请求

```javascript
axios.request({
  url: "http://123.207.32.32:8000/home/multidata",
  method: "get"
}).then(res => {
  console.log("res:", res.data)
})
```



发送`get`请求

```javascript
axios.get(`http://123.207.32.32:9001/lyric?id=500665346`).then(res => {
  console.log("res:", res.data.lrc)
})
axios.get("http://123.207.32.32:9001/lyric", {
  params: {
    id: 500665346
  }
}).then(res => {
  console.log("res:", res.data.lrc)
})
```



发送`post`请求

```javascript
axios.post("http://123.207.32.32:1888/02_param/postjson", {
  name: "coderwhy",
  password: 123456
}).then(res => {
  console.log("res", res.data)
})

// or

axios.post("http://123.207.32.32:1888/02_param/postjson", {
  data: {
    name: "coderwhy",
    password: 123456
  }
}).then(res => {
  console.log("res", res.data)
})
```



### 2.4. 配置公共属性

通过 `axios.defaults.xxx` 进行配置

```javascript
const baseURL = "http://123.207.32.32:8000"

// 给axios实例配置公共的基础配置
axios.defaults.baseURL = baseURL
axios.defaults.timeout = 10000
axios.defaults.headers = {}

axios.get("/home/multidata").then(res => {
  console.log("res:", res.data)
})
```



### 2.5. 发送多个请求

```javascript
axios.all()` 原理也是调用的 `promise.all()
// axios发送多个请求
// Promise.all
axios.all([
  axios.get("/home/multidata"),
  axios.get("http://123.207.32.32:9001/lyric?id=500665346")
]).then(res => {
  console.log("res:", res)
})
```

## 3. axios创建实例

**为什么要创建axios的实例呢?**

*  当我们从axios模块中导入对象时, 使用的实例是默认的实例；
*  当给该实例设置一些默认配置时, 这些配置就被固定下来了.
*  但是后续开发中, 某些配置可能会不太一样；
*  比如**某些请求需要使用特定的**`baseURL`**或者**`timeout`**等.**
*  这个时候, 我们就可以创建新的实例, 并且传入属于该实例的配置信息.

通过`axios.create()`创建一个实例

```javascript
// 创建其他的实例发送网络请求
const instance1 = axios.create({
  baseURL: "http://123.207.32.32:9001",
  timeout: 6000,
  headers: {}
})

instance1.get("/lyric", {
  params: {
    id: 500665346
  }
}).then(res => {
  console.log("res:", res.data)
})

const instance2 = axios.create({
  baseURL: "http://123.207.32.32:8000",
  timeout: 10000,
  headers: {}
})
```

## 4. axios的拦截器

◼ axios的也可以设置拦截器：拦截每次请求和响应

* `axios.interceptors.request.use` (请求成功拦截, 请求失败拦截)
* `axios.interceptors.response.use` (响应成功拦截, 响应失败拦截)

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260917440.png)

注释为拦截器的作用

## 5. axios请求封装

对axios进行一层封装, 今后如果axios不再维护那么我们只需要改HYRequest中的代码即可

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202310260917487.png)