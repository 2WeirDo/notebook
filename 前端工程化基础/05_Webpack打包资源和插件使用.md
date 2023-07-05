## 1. Webpack打包图片

认识asset module type

### 1.1. 资源模块类型(asset module type)

*  `asset/resource` 发送一个单独的文件并导出 URL。
*  `asset/inline` 导出一个资源的 data URI。  
*  `asset/source` 导出资源的源代码 (很少用)
*  `asset` 在导出一个 data URI 和发送一个单独的文件之间自动选择



### 1.2. asset module type的使用

◼ 比如加载图片，我们可以使用下面的方式：

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1684773145461-953b52dd-48be-4c5e-b8b0-44de05f89d71.png)

◼ 但是，如何可以自定义文件的输出路径和文件名呢？

*  方式一：修改output，添加assetModuleFilename属性；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1684773605753-12377b1d-b43b-4a0c-aac2-db567a573792.png)

*  方式二：在Rule中，添加一个generator属性，并且设置filename； (推荐这个)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1684773675424-c7338409-3737-4c78-b7f1-a9409a138328.png)

*  [ext]： 处理文件的扩展名；
*  [name]：处理文件的名称；
*  [hash]：文件的内容，使用MD4的散列函数处理，生成的一个128位的hash值（32个十六进制）；



### 1.3. url-loader的limit效果

◼ 开发中我们往往是小的图片需要转换，但是大的图片直接使用图片即可

*  这是因为小的图片转换base64之后可以和页面一起被请求，减少不必要的请求过程；
*  而大的图片也进行转换，反而会影响页面的请求速度；

◼ 我们需要两个步骤来实现：

*  步骤一：将type修改为asset；
*  步骤二：添加一个parser属性，并且制定dataUrl的条件，添加maxSize属性；

```javascript
rules: [
      {
        test: /\.(png|jpe?g|svg|gif)$/,
        // 1.打包两张图片, 并且这两张图片有自己的地址, 将地址设置到img/bgi中
        // 缺点: 多图片加载的两次网络请求
        // type: "asset/resource",

        // 2.将图片进行base64的编码, 并且直接编码后的源码放到打包的js文件中
        // 缺点: 造成js文件非常大, 下载js文件本身消耗时间非常长, 造成js代码的下载和解析/执行时间过长
        // type: "asset/inline"

        // 3.合理的规范:
        // 3.1.对于小一点的图片, 可以进行base64编码
        // 3.2.对于大一点的图片, 单独的图片打包, 形成url地址, 单独的请求这个url图片
        type: "asset",
        parser: {
          dataUrlCondition: {
            maxSize: 60 * 1024
          }
        },
        generator: {
          // 占位符
          // name: 指向原来的图片名称
          // ext: 扩展名
          // hash: webpack生成的hash
          filename: "img/[name]_[hash:8][ext]"
        }
      },
    ]
```







## 2. Babel和babel-loader

### 2.1. 为什么需要babel？

*  Babel是一个工具链，主要用于旧浏览器或者环境中将ECMAScript 2015+代码转换为向后兼容版本的JavaScript；
*  包括：语法转换、源代码转换等；



◼ babel本身可以作为一个独立的工具（和postcss一样），不和webpack等构建工具配置来单独使用。

◼ 如果我们希望在命令行尝试使用babel，需要安装如下库：

*  @babel/core：babel的核心代码，必须安装；
*  @babel/cli：可以让我们在命令行使用babel；

```
npm install @babel/cli @babel/core -D
```

◼ 使用babel来处理我们的源代码：

*  src：是源文件的目录；
*  --out-dir：指定要输出的文件夹dist

```
npx babel src --out-dir dist
```





### 2.2. 插件的使用

一般用预设就行

◼ 比如我们需要转换箭头函数，那么我们就可以使用箭头函数转换相关的插件：

```javascript
npm install @babel/plugin-transform-arrow-functions -D
npx babel src --out-dir dist --plugins=@babel/plugin-transform-arrow-functions
```

◼ 查看转换后的结果：我们会发现 const 并没有转成 var

*  这是因为 plugin-transform-arrow-functions，并没有提供这样的功能；
*  我们需要使用 plugin-transform-block-scoping 来完成这样的功能；

```javascript
npm install @babel/plugin-transform-block-scoping -D
npx babel src --out-dir dist --plugins=@babel/plugin-transform-block-scoping
,@babel/plugin-transform-arrow-functions
```



### 2.3. Babel的预设preset 插件

◼ 但是如果要转换的内容过多，一个个设置是比较麻烦的，我们可以使用预设（preset）：

◼ 安装@babel/preset-env预设：

```
npm install @babel/preset-env -D
```

◼ 执行如下命令：

```
npx babel src --out-dir dist --presets=@babel/preset-env
```





### 2.4. babel-loader

◼ 在实际开发中，我们通常会在构建工具中通过配置babel来对其进行使用的，比如在webpack中。

◼ 那么我们就需要去安装相关的依赖：

*  如果之前已经安装了@babel/core，那么这里不需要再次安装

```
npm install babel-loader -D
```

◼ 我们可以设置一个规则，在加载js文件时，使用我们的babel

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1684774247521-cef481c9-56dd-41f1-a7ed-ab9d284b7963.png)



### 2.5. babel-preset

◼ 如果我们一个个去安装使用插件，那么需要手动来管理大量的babel插件，我们可以直接给webpack提供一个preset，webpack会根据我们的预设来加载对应的插件列表，并且将其传递给babel

◼ 安装preset-env：

```
npm install @babel/preset-env
```



也可以创建一个配置文件 `babel.config.js` 来存放插件

```javascript
module.exports = {
  // plugins: [
  //   "@babel/plugin-transform-arrow-functions",
  //   "@babel/plugin-transform-block-scoping"
  // ]
  presets: [
    "@babel/preset-env"
  ]
}
 {
        test: /\.js$/,
        use: [
          { 
            loader: "babel-loader", 
          }
        ]
      },
```





## 3. Webpack常见的插件和模式

### 3.1. 认识Plugin

 Loader是用于特定的模块类型进行转换；

 Plugin可以用于执行更加广泛的任务，比如打包优化、资源管理、环境变量注入等；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1684776081780-4a940f29-65ba-440d-a9df-f47e05963ff7.png)

### 3.2. CleanWebpackPlugin

每次修改了一些配置，重新打包时，都需要手动删除dist文件夹：

 我们可以借助于一个插件来帮助我们完成，这个插件就是`CleanWebpackPlugin`；

```
npm install clean-webpack-plugin -D
```

然后配置

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1684776089942-f58b6a36-aaa2-42e7-92d9-1bac4a3c15c0.png)

### 3.3. HtmlWebpackPlugin

◼ 另外还有一个不太规范的地方：

*  我们的HTML文件是编写在根目录下的，而最终打包的dist文件夹中是没有index.html文件的。
*  在进行项目部署的时，必然也是需要有对应的入口文件index.html；
*  所以我们也需要对index.html进行打包处理；



◼ 对HTML进行打包处理我们可以使用另外一个插件：HtmlWebpackPlugin；

```
npm install html-webpack-plugin -D
```

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1684776123678-dcca35dc-83bd-495d-8b61-6bcea026cfa5.png)



### 3.4. 生成index.html分析

◼ 我们会发现，现在自动在dist文件夹中，生成了一个index.html的文件：

*  该文件中也自动添加了我们打包的bundle.js文件

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1684775975601-b38191f3-8752-4ef8-aeeb-d76baa30b872.png)

◼ 这个文件是如何生成的呢？

*  默认情况下是根据ejs的一个模板来生成的；
*  在html-webpack-plugin的源码中，有一个default_index.ejs模块；

### 3.5. 自定义HTML模板

◼ 如果我们想在自己的模块中加入一些比较特别的内容：

*  比如添加一个noscript标签，在用户的JavaScript被关闭时，给予响应的提示；
*  比如在开发vue或者react项目时，我们需要一个可以挂载后续组件的根标签 <div id="app"></div>；

◼ 这个我们需要一个属于自己的index.html模块

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1684776006870-0100a816-a48d-45d9-9c49-7654918f8be2.png)



### 3.6. 自定义模板数据填充

◼ 上面的代码中，会有一些类似这样的语法<% 变量 %>，这个是EJS模块填充数据的方式。

◼ 在配置HtmlWebpackPlugin时，我们可以添加如下配置：

*  template：指定我们要使用的模块所在的路径；
*  title：在进行htmlWebpackPlugin.options.title读取时，就会读到该信息；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1684776060056-5e02cc7a-91af-4930-bb45-597aa8d4a6d7.png)

### 3.7. DefinePlugin的使用

◼ 但是，这个时候编译还是会报错，因为在我们的模块中还使用到一个`BASE_URL`的常量：

◼ 这是因为在编译template模块时，有一个BASE_URL：

*  <link rel="icon" href="<%= BASE_URL %>favicon.ico">；
*  但是我们并没有设置过这个常量值，所以会出现没有定义的错误；

◼ 这个时候我们可以使用`DefinePlugin`插件；

◼ DefinePlugin允许在编译时创建配置的全局常量，是一个webpack内置的插件（不需要单独安装）：

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1684776183320-864c8e1f-ecbe-4fa8-be7a-4324dd964929.png)

◼ 这个时候，编译template就可以正确的编译了，会读取到BASE_URL的值；



### 3.8. Mode配置

◼ 前面我们一直没有讲mode。

◼ Mode配置选项，可以告知webpack使用相应模式的内置优化：

*  默认值是`production`（什么都不设置的情况下）；
*  可选值有：'none' | 'development' | 'production'；

◼ 这几个选项有什么样的区别呢？

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1684776267414-27ec40dd-13c7-4f60-ada8-c9b50db1a66d.png)



## 4. Webpack打包Vue

了解

```javascript
const path = require("path")
const { VueLoaderPlugin } = require("vue-loader/dist/index")

module.exports = {
  entry: "./src/main.js",
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "./build"),
  },
//   webpack解析文件路径：  
//   ◼ 如果是一个文件：
//      如果文件具有扩展名，则直接打包文件；
//      否则，将使用 resolve.extensions选项作为文件扩展名解析；
//   ◼ 如果是一个文件夹：
//      会在文件夹中根据 resolve.mainFiles配置选项中指定的文件顺序查找；
//       ✓ resolve.mainFiles的默认值是 ['index']；
//       ✓ 再根据 resolve.extensions来解析扩展名；
  resolve: {
    extensions: [".js", ".json", ".vue", ".jsx", ".ts", ".tsx"],
    alias: {
      utils: path.resolve(__dirname, "./src/utils")
    }
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [ "style-loader", "css-loader", "postcss-loader" ]
      },
      {
        test: /\.less$/,
        use: [ "style-loader", "css-loader", "less-loader", "postcss-loader" ]
      },
      {
        test: /\.(png|jpe?g|svg|gif)$/,

        type: "asset",
        parser: {
          dataUrlCondition: {
            maxSize: 60 * 1024
          }
        },
        generator: {
          filename: "img/[name]_[hash:8][ext]"
        }
      },
      {
        test: /\.js$/,
        use: [
          { 
            loader: "babel-loader", 
          }
        ]
      },
      {
        test: /\.vue$/,
        loader: "vue-loader"
      }
    ]
  },
  plugins: [
    new VueLoaderPlugin()
  ]
}
```



这个配置文件是用于Webpack的配置文件，它定义了项目的入口、输出路径、模块处理规则和插件等。

1.  entry: 定义了项目的入口文件，即打包的起点。在这个例子中，入口文件是"./src/main.js"。 
2.  output: 定义了打包后的输出配置。包括生成的文件名、输出路径等。在这个例子中，输出文件名是"bundle.js"，输出路径是"./build"。 
3.  resolve: **定义模块解析规则**。extensions属性指定了可以省略的文件扩展名，这样在引入模块时可以不写后缀。alias属性用于创建模块的别名，可以简化模块引用路径。在这个例子中，定义了一些常见的扩展名和别名。 
4.  module: 定义模块处理规则。rules属性是一个数组，每个元素都是一个模块处理规则对象。在这个例子中，定义了几个不同的规则： 

* * 处理.css文件的规则：使用"style-loader"、"css-loader"和"postcss-loader"依次处理.css文件。
  * 处理.less文件的规则：使用"style-loader"、"css-loader"、"less-loader"和"postcss-loader"依次处理.less文件。
  * 处理图片文件的规则：使用"asset"进行处理，根据图片大小进行不同的处理方式。小于60KB的图片会被编码为base64格式，大于60KB的图片会被单独打包并生成一个URL地址。
  * 处理.js文件的规则：使用"babel-loader"进行处理，可以通过options属性配置Babel的插件和选项。
  * 处理.vue文件的规则：使用"vue-loader"进行处理，用于处理Vue单文件组件。

1.  plugins: 定义插件配置。在这个例子中，使用了VueLoaderPlugin插件，它用于处理Vue单文件组件。 



## 5. resolve模块解析

◼ extensions是解析到文件时自动添加扩展名：

*  默认值是 ['.wasm', '.mjs', '.js', '.json']；
*  所以如果我们代码中想要添加加载 .vue 或者 jsx 或者 ts 等文件时，我们必须自己写上扩展名；

◼ 另一个非常好用的功能是**配置别名**`**alias**`：

*  特别是当我们项目的目录结构比较深的时候，或者一个文件的路径可能需要` ../../../`这种路径片段；
*  我们可以给某些常见的路径起一个别名；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1684775649661-bd2a8866-b75f-4e41-acf5-c6acf3523306.png)

```javascript
const path = require("path")
const { VueLoaderPlugin } = require("vue-loader/dist/index")
const { CleanWebpackPlugin } = require("clean-webpack-plugin")
const HtmlWebpackPlugin = require('html-webpack-plugin')
const { DefinePlugin } = require("webpack")

module.exports = {
  mode: "production",
  entry: "./src/main.js",
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "./build"),
    clean: true
  },
//   webpack解析文件路径：
//   ◼ 如果是一个文件：
//      如果文件具有扩展名，则直接打包文件；
//      否则，将使用 resolve.extensions选项作为文件扩展名解析；
//   ◼ 如果是一个文件夹：
//      会在文件夹中根据 resolve.mainFiles配置选项中指定的文件顺序查找；
//       ✓ resolve.mainFiles的默认值是 ['index']；
//       ✓ 再根据 resolve.extensions来解析扩展名；
  resolve: {
    extensions: [".js", ".json", ".vue", ".jsx", ".ts", ".tsx"],
    alias: {
      utils: path.resolve(__dirname, "./src/utils")
    }
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [ "style-loader", "css-loader", "postcss-loader" ]
      },
      {
        test: /\.less$/,
        use: [ "style-loader", "css-loader", "less-loader", "postcss-loader" ]
      },
      {
        test: /\.(png|jpe?g|svg|gif)$/,

        type: "asset",
        parser: {
          dataUrlCondition: {
            maxSize: 60 * 1024
          }
        },
        generator: {

          filename: "img/[name]_[hash:8][ext]"
        }
      },
      {
        test: /\.js$/,
        use: [
          { 
            loader: "babel-loader", 

          }
        ]
      },
      {
        test: /\.vue$/,
        loader: "vue-loader"
      }
    ]
  },
  plugins: [
    new VueLoaderPlugin(),
    // new CleanWebpackPlugin(),  如果 output 中 clean 为 true 则可以不设置
    new HtmlWebpackPlugin({
      title: "电商项目",
      template: "./index.html"
    }),
    new DefinePlugin({
      BASE_URL: "'./'",  // 这个不设置vue的那个默认页面要报错
      coderwhy: "'why'",
      counter: "123"
    })
  ]
}
```



这个文件与上一个文件相比有以下几点不同：

1.  添加了`mode`字段：在这个配置文件中，`mode`字段被设置为`development`，表示开发模式。它会为你提供一些用于开发环境的默认配置，例如开启代码映射和热模块替换等。 
2.  添加了`devServer`字段：这个字段用于配置开发服务器的选项。在这个例子中，注释掉了一些选项，包括`hot`表示开启热模块替换，`host`表示服务器的主机地址，`port`表示服务器的端口号，`open`表示是否自动打开浏览器，以及`compress`表示是否启用gzip压缩。 
3.  添加了额外的插件：在`plugins`数组中添加了其他的插件配置。 

* * `CleanWebpackPlugin`：用于在每次构建之前清理输出目录。
  * `HtmlWebpackPlugin`：根据提供的模板生成一个HTML文件，并将打包后的脚本文件自动注入到HTML中。
  * `DefinePlugin`：用于定义这个文件与上一个文件相比有以下几点不同：



## 