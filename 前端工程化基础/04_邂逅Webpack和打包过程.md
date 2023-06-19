## 1. 内置模块path

 path模块用于对路径和文件进行处理，提供了很多好用的方法。

*  window上会使用 `\`或者 `\\` 来作为文件路径的分隔符，当然目前也支持 `/`；
*  在Mac OS、Linux的Unix操作系统上使用 `/` 来作为文件路径的分隔符；



path常见的API

◼ 从路径中获取信息

*  dirname：获取文件的父文件夹；
*  basename：获取文件名；
*  extname：获取文件扩展名；

◼ 路径的拼接：`path.join`

*  如果我们希望将多个路径进行拼接，但是不同的操作系统可能使用的是不同的分隔符；
*  这个时候我们可以使用path.join函数；

◼ 拼接绝对路径：`path.resolve` 🔥

*  path.resolve() 方法会把一个路径或路径片段的序列解析为一个绝对路径；
*  给定的路径的序列是从右往左被处理的，后面每个 path 被依次解析，直到构造完成一个绝对路径；
*  如果在处理完所有给定path的段之后，还没有生成绝对路径，则使用当前工作目录；
*  生成的路径被规范化并删除尾部斜杠，零长度path段被忽略；
*  如果没有path传递段，path.resolve()将返回当前工作目录的绝对路径；

```javascript
const path = require("path")
const filepath = "C://abc/cba/nba.txt"
console.log(path.extname(filepath))
console.log(path.basename(filepath))
console.log(path.dirname(filepath))

// .txt
// nba.txt
// C://abc/cba
const path = require("path")
console.log(path.resolve("./abc/cba", "../why/kobe", "./abc.txt"))
console.log(path.resolve())
```

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202306192353179.png)





## 2. 认识webpack

◼webpack是一个静态的模块化 打包工具，为现代的JavaScript应用程序；

◼ 我们来对上面的解释进行拆解：

*  **打包bundler**：webpack可以将帮助我们进行打包，所以它是一个打包工具
*  **静态的static**：这样表述的原因是我们最终可以将代码打包成最终的静态资源（部署到静态服务器）；
*  **模块化module**：webpack默认支持各种模块化开发，ES Module、CommonJS、AMD等；
*  **现代的modern**：我们前端说过，正是因为现代前端开发面临各种各样的问题，才催生了webpack的出现和发展；

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202306192353229.png)







## 3. Webpack的安装

Webpack的运行是依赖Node环境的，所以我们电脑上必须有`Node`环境

◼ webpack的安装目前分为两个：`webpack`、`webpack-cli`

◼ 那么它们是什么关系呢？

webpack-cli 是一个用于在命令行中运行 webpack 的工具

*  执行webpack命令，会执行node_modules下的.bin目录下的webpack；
*  webpack在执行时是依赖webpack-cli的，如果没有安装就会报错；
*  而webpack-cli中代码执行时，才是真正利用webpack进行编译和打包的过程；
*  所以在安装webpack时，我们需要同时安装webpack-cli（第三方的脚手架事实上是没有使用webpack-cli的，而是类似于自己的vue-service-cli的东西）

```javascript
npm install webpack webpack-cli –g // 全局安装
npm install webpack webpack-cli –D // 局部安装, 一般我们用局部安装
```





## 4. webpack默认打包

webpack是如何确定我们的入口的呢？

*  事实上，当我们运行`webpack`时，`webpack`会查找当前目录下的 `src/index.js`作为入口；
*  所以，如果当前项目中没有存在`src/index.js`文件，那么会报错；

◼ 当然，我们也可以通过配置来指定入口和出口

`npx webpack --entry ./src/main.js --output-path ./build` (命令行输入)

创建了一个build文件作为打包后的文件. 入口文件则为src后的main.js



当然, 我们也可以在配置文件`webpack.config.js`中设置

◼ 在通常情况下，webpack需要打包的项目是非常复杂的，并且我们需要一系列的配置来满足要求，默认配置必然是不可以的。

◼ 我们可以在根目录下创建一个webpack.config.js文件，来作为webpack的配置文件：

```javascript
const path = require("path")
module.exports = {
  entry: "./src/main.js",
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "./build")
  }
}
```

入口文件为src下的main.js文件, 打包后是创建了一个build文件夹, 里面有bundle.js作为打包后的文件



## 5. 创建局部的webpack

webpack有点特殊, 但是我们如果要使用局部的webpack, 以防万一还是要加 npx 

◼ 第一步：创建package.json文件，用于管理项目的信息、库依赖等

```
npm init
```

◼ 第二步：安装局部的webpack

```
npm install webpack webpack-cli -D
```

◼ 第三步：使用局部的webpack

```
npx webpack
```

◼ 第四步：在package.json中创建scripts脚本，执行脚本打包即可

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202306192353152.png)

```
npm run build
```



指定配置文件

如果想自己设置 `webpack.config.js`的名字 , 可以先改名然后在scripts中加入后缀即可

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202306192353147.png)

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202306192353158.png)





## 6. Webpack的依赖图

◼ webpack到底是如何对我们的项目进行打包的呢？

*  事实上webpack在处理应用程序时，它会根据命令或者配置文件找到入口文件；
*  从入口开始，会生成一个 依赖关系图，这个依赖关系图会包含应用程序中所需的所有模块（比如.js文件、css文件、图片、字体等）；
*  然后遍历图结构，打包一个个模块（根据文件的不同使用不同的loader来解析）；

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202306192353236.png)



## 7. css-loader的使用

◼`loader`是什么呢？

*  loader 可以用于对模块的源代码进行转换；
*  我们可以将css文件也看成是一个模块，我们是通过import来加载这个模块的；
*  在加载这个模块时，webpack其实并不知道如何对其进行加载，我们必须制定对应的loader来完成这个功能；

◼ 那么我们需要一个什么样的loader呢？

*  对于加载css文件来说，我们需要一个可以读取css文件的loader；
*  这个loader最常用的是css-loader；

◼ css-loader的安装

```
npm install css-loader -D
```



## 8. loader配置方式 🔥

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202306192353418.png)

```javascript
const path = require("path")

module.exports = {
  entry: "./src/main.js",
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "./build")
  },
  module: {
    rules: [
      {
        // 告诉webpack匹配什么文件
        test: /\.css$/,
        // use: [ // use中多个loader的使用顺序是从后往前
        //   { loader: "style-loader" },
        //   { loader: "css-loader" }
        // ],
        // 简写一: 如果loader只有一个
        // loader: "css-loader"
        // 简写二: 多个loader不需要其他属性时, 可以直接写loader字符串形式
        use: [ 
          "style-loader",
          "css-loader", 
          "postcss-loader"
          // {
          //   loader: "postcss-loader",
          //   options: {
          //     postcssOptions: {
          //       plugins: [
          //         "autoprefixer"
          //       ]
          //     }
          //   }
          // }
        ]
      },
      {
        test: /\.less$/,
        use: [ "style-loader", "css-loader", "less-loader", "postcss-loader" ]
      }
    ]
  }
}
```



## 9. 认识style-loader

◼ 我们已经可以通过css-loader来加载css文件了

*  但是你会发现这个css在我们的代码中并没有生效（页面没有效果）。

◼ 这是为什么呢？

*  因为css-loader只是负责将.css文件进行解析，并不会将解析之后的css插入到页面中；
*  如果我们希望再完成插入style的操作，那么我们还需要另外一个loader，就是`style-loader`；

◼ 安装style-loader：

```
npm install style-loader -D
```



## 10. 配置style-loader

◼ 那么我们应该如何使用`style-loader`：

*  在配置文件中，添加`style-loader`；
*  注意：因为`loader`的执行顺序是从右向左（或者说从下到上，或者说从后到前的），所以我们需要将`style-loader`写到`css-loader`的前面；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1684771376756-c0f0a72f-6bba-4077-bafe-4ccafaa306f3.png)

重新执行编译npm run build，可以发现打包后的css已经生效了：

*  当前目前我们的css是通过页内样式的方式添加进来的；
*  后续我们也会讲如何将css抽取到单独的文件中，并且进行压缩等操作；



## 11. less-loader处理

◼ 在开发中，我们可能会使用less、sass、stylus的预处理器来编写css样式，效率会更高。

◼ 那么，如何可以让我们的环境支持这些预处理器呢？

*  首先我们需要确定，less、sass等编写的css需要通过工具转换成普通的css；

◼ 但是在项目中我们会编写大量的css，它们如何可以自动转换呢？

*  这个时候我们就可以使用`less-loader`，来自动使用less工具转换less到css；

```
npm install less-loader -D
```

webpack.config.js

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1684771620316-4775f3be-383c-4f43-a9b7-19091fcaaf98.png)



## 12. 认识PostCSS工具

◼ 什么是PostCSS呢？

*  PostCSS是一个通过JavaScript来转换样式的工具；
*  这个工具可以帮助我们进行一些CSS的转换和适配，比如自动添加浏览器前缀、css样式的重置；
*  但是实现这些功能，我们需要借助于PostCSS对应的插件；

◼ 如何使用PostCSS呢？主要就是两个步骤：

*  第一步：查找PostCSS在构建工具中的**扩展**，比如webpack中的postcss-loader；
*  第二步：选择可以添加你需要的PostCSS相关的插件；





## 13. postcss-loader

◼ 我们可以借助于构建工具：

*  在webpack中使用postcss就是使用postcss-loader来处理的；

◼ 我们来安装postcss-loader：

```
npm install postcss-loader -D
```

◼我们修改加载css的loader：

*  注意：因为postcss需要有对应的插件才会起效果，所以我们需要配置它的plugin；

webpack.config.js

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202306192353262.png)



## 14. 单独的postcss配置文件

◼ 因为我们需要添加前缀，所以要安装autoprefixer：

```
npm install autoprefixer -D
```

◼ 我们可以将这些配置信息放到一个单独的文件中进行管理：

*  在根目录下创建`postcss.config.js`
*  在文件中写以下字句就行 ,预设啥都有, 不用一个一个安装

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1684771909315-b58d4c48-5c25-40fd-853f-769c66eabe79.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1684772149481-e0a36721-6a60-4962-92dd-e679fbaa7939.png)





## 15. postcss-preset-env

◼ 事实上，在配置postcss-loader时，我们配置插件并不需要使用autoprefixer。

◼ 我们可以使用另外一个插件：`postcss-preset-env`

*  postcss-preset-env也是一个postcss的插件；
*  它可以帮助我们将一些现代的CSS特性，转成大多数浏览器认识的CSS，并且会根据目标浏览器或者运行时环境添加所需的polyfill；
*  也包括会自动帮助我们添加autoprefixer（所以相当于已经内置了autoprefixer）；

◼ 首先，我们需要安装postcss-preset-env：

```
npm install postcss-preset-env -D
```

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202306192353684.png)

注意：我们在使用某些postcss插件时，也可以直接传入字符串 , 下面和上面一样的

postcss.config.js

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202306192353677.png)