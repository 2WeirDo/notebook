## 1. TypeScript模块化

◼ 我们需要先理解 `TypeScript` 认为什么是一个模块 .

*  `JavaScript` 规范声明任何没有 `export` 的 `JavaScript` 文件都应该被认为是一个脚本，而非一个模块。
*  在一个脚本文件中，变量和类型会被声明在共享的全局作用域，将多个输入文件合并成一个输出文件，或者在 HTML使用多个 <script> 标签加载这些文件。

◼ 如果你有一个文件，现在没有任何 import 或者 export，但是你希望它被作为模块处理，添加这行代码：

```typescript
export {}
```

◼ 这会把文件改成一个**没有导出任何内容的模块**，这个语法可以生效，无论你的模块目标是什么



**第三方包如果没有对应的声明文件应该如何进行处理?**

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202309111350551.png)



在`ts.config.json`中`"jsx": "preserve"` :

"`preserve`" 意味着在处理 `JSX` 时保留某些特定属性、语法结构或行为，以确保它们在渲染、编译或其他处理过程中不被修改、移除或转换。



你需要使用 `type` 前缀 ，表明被导入的是一个类型：

```typescript
import { price, date } from "./utils/format";
import { sum } from "./utils/math";

// 导入的是类型, 推荐在类型的前面加上type关键
import type { IDType, IPerson } from "./utils/type"
// 方便在转为js时候删除这行代码, 使这个导入被安全移除
// 这些可以让一个非 TypeScript 编译器比如 Babel、swc 或者 esbuild 知道什么样的导入可以被安全移除, 提高编译速度

console.log(sum(20, 30))

const id1: IDType = 111
const p: IPerson = { name: "why", age: 18 }


// 使用命名空间中的内容
price.format("1111")
date.format("22222")
```



类型声明文件

```typescript
export interface IPerson {
  name: string
  age: number
}

export type IDType = number | string

// 这里面不写逻辑代码
```





## 2. TypeScript命名空间 

已经不推荐使用

 虽然命名空间没有被废弃，但是由于 ES 模块已经拥有了命名空间的大部分特性，因此更推荐使用 ES 模块，这样才能与JavaScript 的（发展）方向保持一致

```typescript
export namespace price {
  // 命名空间内的东西也要导出
  export function format(price: string) {
    return "¥" + price
  }
  export const name = "price"
}

export namespace date {
  export function format(dateString) {
    return "2022-10-10"
  }
  const name = "date"
}
```

## 3. 类型的查找

◼ 我们这里先给大家介绍另外的一种`typescript`文件：`.d.ts`文件

*  我们之前编写的`typescript`文件都是 `.ts` 文件，这些文件最终会输出 `.js` 文件，也是我们通常编写代码的地方；
*  还有另外一种文件 `.d.ts `文件，它是用来做类型的声明(`declare`)，称之为类型声明（`TypeDeclaration`）或者类型定义（`Type Definition`）文件。
*  它仅仅用来做类型检测，告知`typescript`我们有哪些类型；

◼ 那么`typescript`会在哪里查找我们的类型声明呢？

*  内置类型声明；
*  外部定义类型声明；
*  自己定义类型声明；

### 3.1. 内置类型声明

◼ 内置类型声明是typescript自带的、帮助我们内置了JavaScript运行时的一些标准化API的声明文件；

*  包括比如Function、String、Math、Date等内置类型；
*  也包括运行环境中的DOM API，比如Window、Document等；

◼ 内置类型声明通常在我们安装`typescript`的环境中会带有的；

◼ TypeScript 使用模式命名这些声明文件`lib.[something].d.ts`

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202309111350922.png)

◼ 我们可以通过`target`和`lib`来决定哪些内置类型声明是可以使用的：

*  例如，`startsWith`字符串方法只能从称为`ECMAScript 6`的 `JavaScript` 版本开始使用；

◼ 我们可以通过在`tsconfig.json`中`target`的编译选项来配置：`TypeScript`通过`lib`根据您的`target`设置更改默认包含的文件来帮助解决此问题。

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202309111350852.png)



### 3.2. 第三方库

◼ 外部类型声明通常是我们使用一些库（比如第三方库）时，需要的一些类型声明。

这些库通常有两种类型声明方式：

◼ 方式一：在自己库中进行类型声明（编写`.d.ts`文件），比如`axios`

◼ 方式二：通过社区的一个公有库`DefinitelyTyped`存放类型声明文件

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202309111350933.png)



### 3.3. 自定义声明

◼ 什么情况下需要自己来定义声明文件呢？

*  情况一：我们使用的第三方库是一个纯的`JavaScript`库，没有对应的声明文件；比如`lodash`
*  情况二：我们给自己的代码中声明一些类型，方便在其他地方直接进行使用；



◼ 在某些情况下，我们也可以声明文件：

 我们也可以声明模块，比如lodash模块默认不能使用的情况，可以自己来声明这个模块：

 比如在开发vue的过程中，默认是不识别我们的.vue文件的，那么我们就需要对其进行文件的声明；

 比如在开发中我们使用了 jpg 这类图片文件，默认typescript也是不支持的，也需要对其进行声明；

 比如我们在index.html中直接引入了jQuery： 我们可以进行命名空间的声明



```typescript
// 我们也可以声明模块，比如lodash模块默认不能使用的情况，可以自己来声明这个模块：
declare module "lodash" {
  export function join(...args: any[]): any
}

// 为自己的 变量/函数/类 定义类型声明
declare const whyName: string
declare const whyAge: number
declare const whyHeight: number

declare function foo(bar: string): string

declare class Person {
  constructor(public name: string, public age: number)
}

// 作为一个第三方库为其他开发者提供类型声明文件 .d.ts => axios.d.ts


// 声明文件模块
// 让某些ts不能引入的模块可以引入
// 也就是ts不支持这类文件, 我们需要对其进行声明
declare module "*.png"
declare module "*.jpg"
declare module "*.jpeg"
declare module "*.svg"

declare module "*.vue"


// 声明成模块(不合适)

// 声明命名空间 (CDN引入的情况下)
declare namespace $ {
  export function ajax(settings: any): any
}
import { sum } from "./utils/math"
import axios from "axios"
import type { AxiosRequestConfig, AxiosInstance } from "axios"
import React from "react"
import _ from "lodash"
import KobeImage from "./img/kobe02.png"
import App from "./vue/App.vue"

const message: string = "Hello World"
console.log(message.length, message)
console.log(sum(20, 30))

// lib.dom.d.ts
const h2El = document.createElement("h2")
h2El.textContent = "Hello TypeScript"
document.body.append(h2El)

// lib.es2015.d.ts
const promise = new Promise((resolve, reject) => {})
console.log(message.startsWith("Hello"))


// axios

// lodash
console.log(_.join(["abc", "cba"]))


// 给自己的代码添加类型声明文件
// 平时使用的代码中用到的类型, 直接在当前位置进行定义或者在业务文件夹某一个位置编写一个类型文件即可
type IDType = number | string
interface IKun {
  name: string
  age: number
  slogan: string
}

const id1: IDType = 123
// id1 = true


// 需要编写类型声明
console.log(whyName, whyAge, whyHeight)
console.log(foo("why"))

const p = new Person("kobe", 30)
console.log(p.name, p.age)


// 图片文件的使用
const imgEl = document.createElement("img")
imgEl.src = KobeImage
document.body.append(imgEl)


// jquery
$.ajax({
  url: "http://codercba.com:8000/home/multidata",
  success: function(res: any) {
    console.log(res)
  }
})
{
  "name": "ts_demo",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "serve": "webpack serve",
    "build": "webpack"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "@types/react": "^18.0.21",
    "html-webpack-plugin": "^5.5.0",
    "ts-loader": "^9.4.1",
    "webpack": "^5.88.2",
    "webpack-cli": "^4.10.0",
    "webpack-dev-server": "^4.11.1"
  },
  "dependencies": {
    "axios": "^1.1.2",
    "lodash": "^4.17.21",
    "react": "^18.2.0"
  }
}
const path = require("path")
const HtmlWeabpckPlugin = require("html-webpack-plugin")

module.exports = {
  mode: "development",
  entry: "./src/index.ts",
  output: {
    path: path.resolve(__dirname, "./dist"),
    filename: "bundle.js"
  },
  resolve: {
    extensions: [".ts", ".js", ".cjs", ".json"]
  },
  devServer: {},
  module: {
    rules: [
      {
        test: /\.ts$/,
        loader: "ts-loader"
      },
      {
        test: /\.(png|jpe?g|svg|gif)$/,
        type: "asset/resource"
      }
    ]
  },
  plugins: [
    new HtmlWeabpckPlugin({
      template: "./index.html"
    })
  ]
}
```





## 4. 认识tsconfig.json

◼ `tsconfig.json`文件有两个作用：

 作用一（主要的作用）：

**让TypeScript Compiler在编译的时候，知道如何去编译TypeScript代码和进行类型检测；**

* 比如是否允许不明确的`this`选项，是否允许隐式的`any`类型；
* 将`TypeScript`代码编译成什么版本的`JavaScript`代码；

 作用二：

**让编辑器（比如VSCode）可以按照正确的方式识别TypeScript代码；**

* 对于哪些语法进行提示、类型错误检测等等；



`JavaScript` 项目可以使用` jsconfig.json` 文件，它的作用与 `tsconfig.json` 基本相同，只是默认启用了一些 `JavaScript` 相关的编译选项



## 5. 常见配置

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202309111350948.png)

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202309111350567.png)

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202309111350517.png)

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202309111350533.png)





## 6. TypeScript封装axios

拦截器的使用 : 

1. token
2. 修改配置
3. Loading



全局请求 (全局拦截器)

src/service/request/index.ts

```typescript
import axios from "axios"
import type { AxiosInstance, AxiosRequestConfig, AxiosResponse } from "axios"
import type { HYRequestConfig } from "./type"

// 拦截器: 蒙版Loading/token/修改配置

/**
 * 两个难点:
 *  1.拦截器进行精细控制
 *    > 全局拦截器
 *    > 实例拦截器
 *    > 单次请求拦截器
 * 
 *  2.响应结果的类型处理(泛型的问题)
 */

// 这里将其封装为一个类，而不是一个函数的原因是因为 : 
// 类可以创建多个实例，适用范围更广，封装性更强一些。
class HYRequest {
  instance: AxiosInstance

  // request实例 => axios的实例
  constructor(config: HYRequestConfig) {
    this.instance = axios.create(config)

    // 每个instance实例都添加拦截器
    this.instance.interceptors.request.use(config => {
      // loading/token
      console.log("全局请求成功的拦截")
      return config
    }, err => {
      console.log("全局请求失败的拦截")
      return err
    })
    this.instance.interceptors.response.use(res => {
      console.log("全局响应成功的拦截")
      // 我们对接口请求的数据主要是存在在.data中，跟data同级的属性我们基本是不需要的。
      return res.data
    }, err => {
      console.log("全局响应失败的拦截")
      return err
    })

    // 针对特定的hyRequest实例添加拦截器
    // 如果有传, 则添加特定拦截器
    this.instance.interceptors.request.use(
      config.interceptors?.requestSuccessFn,
      config.interceptors?.requestFailureFn
    )
    this.instance.interceptors.response.use(
      config.interceptors?.responseSuccessFn,
      config.interceptors?.responseFailureFn
    )
  }

  // 封装网络请求的方法
  // T => IHomeData
  request<T = any>(config: HYRequestConfig<T>) {
    // 单次请求的成功拦截处理
    if (config.interceptors?.requestSuccessFn) {
      config = config.interceptors.requestSuccessFn(config)
    }

    // 返回Promise
    return new Promise<T>((resolve, reject) => {
      this.instance.request<any, T>(config).then(res => {
        // 通过看源码第一个传入的是any不用变, 第二个传入我们想要的类型
        // 单词响应的成功拦截处理
        if (config.interceptors?.responseSuccessFn) {
          res = config.interceptors.responseSuccessFn(res)
        }
        resolve(res)
      }).catch(err => {
        reject(err)
      })
    })
  }

  get<T = any>(config: HYRequestConfig<T>) {
    return this.request({ ...config, method: "GET" })
  }
  post<T = any>(config: HYRequestConfig<T>) {
    return this.request({ ...config, method: "POST" })
  }
  delete<T = any>(config: HYRequestConfig<T>) {
    return this.request({ ...config, method: "DELETE" })
  }
  patch<T = any>(config: HYRequestConfig<T>) {
    return this.request({ ...config, method: "PATCH" })
  }
}


export default HYRequest
import type { AxiosRequestConfig, AxiosResponse } from "axios"

// 针对AxiosRequestConfig配置进行扩展
export interface HYInterceptors<T = AxiosResponse> {
  requestSuccessFn?: (config: AxiosRequestConfig) => AxiosRequestConfig 
  requestFailureFn?: (err: any) => any 
  responseSuccessFn?: (res: T) => T
  responseFailureFn?: (err: any) => any 
}

//
export interface HYRequestConfig<T = AxiosResponse> extends AxiosRequestConfig {
  interceptors?: HYInterceptors<T>
}
```



封装一个实例  (实例请求)

service/index.ts

```typescript
import { BASE_URL, TIME_OUT } from "./config";
// 略

import HYRequest from "./request";

const req = new HYRequest({
  baseURL: BASE_URL,
  timeout: TIME_OUT,
  // 这个请求就没有添加拦截器, 用的就是默认拦截器
})

export const hyRequest = new HYRequest({
  baseURL: "http://codercba.com:1888/airbnb/api",
  timeout: 8000,

  // 传递特定拦截器
  // 因为原本传递的类型中没有interceptors这个属性, 我们就要针对于原本类型进行拓展
  interceptors: {
    requestSuccessFn: (config) => {
      console.log("爱彼迎的请求成功的拦截")
      return config
    },
    requestFailureFn: (err) => {
      console.log("爱彼迎的请求失败的拦截")
      return err
    },
    responseSuccessFn: (res) => {
      console.log("爱彼迎的响应成功的拦截")
      return res
    },
    responseFailureFn: (err) => {
      console.log("爱彼迎的响应失败的拦截")
      return err
    }
  }
})

export default hyRequest
```



单次请求

service/modules/entire.ts

```typescript
import { hyRequest } from "..";


hyRequest.request({
  url: "/entire/list",
  params: {
    offset: 0,
    size: 20
  }
}).then(res => {
  console.log(res)
})

// 自己封装一个返回类型
interface IHighScoreData {
  list: any[],
  subtitle: string,
  title: string
  type: string,
  _id: string
}
hyRequest.request<IHighScoreData>({
  url: "/home/highscore",
  interceptors: {
    requestSuccessFn: (config) => {
      console.log("/home/highscore请求成功的拦截")
      return config
    },
    responseSuccessFn: (res) => {
      console.log("/home/highscore响应成功的拦截")
      return res
    }
  }
}).then(res => {
  console.log(res.list, res.subtitle, res.title)
})
```

home.ts

```typescript
import hyRequest from "..";

// 发送网络请求
// hyRequest.post
// 自己封装一个返回的数据类型
interface IHomeData {
  data: any,
  returnCode: string,
  success: boolean
}

hyRequest.request<IHomeData>({
  url: "/home/multidata"
}).then(res => {
  console.log(res.data, res.success, res.returnCode)
})
```





![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202309111350418.png)

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202309111350499.png)



我懒得记笔记hhh, 刚好掘金上有相关详细完善的笔记, 我就引用了

如有不懂的可自行阅读文章

https://juejin.cn/post/7071518211392405541?searchId=20230817144112CF2EA03E64B227185CF8