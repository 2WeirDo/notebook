## 1. Mustache语法

◼ 如果我们希望把数据显示到模板（template）中，使用最多的语法是 “Mustache”语法 (双大括号) 的文本插值。

* 并且我们前端提到过，data返回的对象是有添加到Vue的响应式系统中；
* 当data中的数据发生改变时，对应的内容也会发生更新。
* 当然，Mustache中不仅仅可以是data中的属性，也可以是一个JavaScript的表达式。

```html
<!-- 1.基本使用 -->
<h2>{{ message }}</h2>
<h2>当前计数: {{ counter }} </h2>

<!-- 2.表达式 -->
<h2>计数双倍: {{ counter * 2 }}</h2>
<h2>展示的信息: {{ info.split(" ") }}</h2>

<!-- 3.三元运算符 -->
<h2>{{ age >= 18? "成年人": "未成年人" }}</h2>

<!-- 4.调用methods中函数 -->
<h2>{{ formatDate(time) }}</h2>
```

◼ 另外这种用法是错误的：

```html
    <!-- 5.注意: 这里不能定义语句 -->
  <h2>{{ const name = "why" }}</h2>
```

## 2. 常见的基本指令 (了解)

### 2.1. v-once指令

◼ `v-once`用于指定元素或者组件只渲染一次：

* 当数据发生变化时，元素或者组件以及其所有的子元素将视为静态内容并且跳过；
* 该指令可以用于**性能优化**；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685257588285-92d17dfc-3090-4233-b4c2-7c1c6fc6902f.png)

◼ 如果是子节点，也是只会渲染一次：

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685257601782-6f2ae1bb-b8f6-4a6a-bf2b-8a81a0e03bea.png)



### 2.2. v-text指令

◼ 用于更新元素的 textContent：

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685257642831-d483163d-c5d3-4163-a440-ac5e7bcfdcc8.png)

明显mustache方便些

### 2.3. v-html指令

◼ 默认情况下，如果我们展示的**内容本身是 html 的**，那么vue并不会对其进行特殊的解析。

* 如果我们希望这个内容被Vue可以解析出来，那么可以使用 v-html 来展示；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685257717057-b24e9ff8-ae1e-42ce-ae92-eec5a1c6e559.png)



### 2.4. v-pre指令

◼ v-pre用于跳过元素和它的子元素的编译过程，显示原始的Mustache标签：

* 跳过不需要编译的节点，加快编译的速度；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685257769430-4d02bc41-16d8-4ec7-b2b4-edf01515f92d.png)



### 2.5. v-cloak指令

当使用直接在 DOM 中书写的模板时，可能会出现一种叫做“未编译模板闪现”的情况：用户可能先看到的是还没编译完成的双大括号标签，直到挂载的组件将它们替换为实际渲染的内容。

◼ 这个指令保持在元素上**直到**关联组件实例**结束编译**。(用于隐藏尚未完成编译的 DOM 模板。)

* 和 CSS 规则如 `[v-cloak] { display: none }` 一起用时，这个指令可以隐藏未编译的 Mustache 标签直到组件实例准备完毕

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685257841539-86a3e4e9-dcfd-4d51-a371-cc542eaef812.png)

直到编译完成前，`<div>` 将不可见。



### 2.6. v-memo指令

缓存一个模板的子树。在元素和组件上都可以使用。为了实现缓存，该指令需要传入一个固定长度的依赖值**数组**进行比较。**如果数组里的每个值都与最后一次的渲染相同，那么整个子树的更新将被跳过**。举例来说：

```html
<div v-memo="[valueA, valueB]">
  ...
</div>
```

当组件重新渲染，如果 valueA 和 valueB 都保持不变，这个 <div> 及其子项的所有更新都将被跳过。实际上，甚至虚拟 DOM 的 vnode 创建也将被跳过，因为缓存的子树副本可以被重新使用。



正确指定缓存数组很重要，否则应该生效的更新可能被跳过。v-memo 传入空依赖数组 (`v-memo="[]"`) 将与 `v-once` 效果相同。

## 3. v-bind绑定属性

◼ 前端讲的一系列指令，主要是将值插入到模板内容中。

◼ 但是，除了内容需要动态来决定外，某些**属性**我们也希望动态来绑定。

*  比如动态绑定a元素的href属性；
*  比如动态绑定img元素的src属性；

◼ v-bind用于绑定一个或多个属性值，或者向另一个组件传递props值（这个学到组件时再介绍）；

◼ 在开发中，有哪些属性需要动态进行绑定呢？

*  还是有很多的，比如图片的链接src、网站的链接href、动态绑定一些类、样式等等

◼ v-bind有一个对应的语法糖，也就是简写方式。`v-bind:`  等同于 `:`







## 4. 绑定class和style

### 4.1. 绑定class – 对象语法

◼ 对象语法：我们可以传给 :class (v-bind:class 的简写) 一个对象，以动态地切换 class。

```html
<div id="app">
  <!-- 1.基本绑定class -->
  <h2 :class="classes">Hello World</h2>

  <!-- 2.动态class可以写对象语法 -->
  <button :class=" isActive ? 'active': '' " @click="btnClick">我是按钮</button>

  <!-- 2.1.对象语法的基本使用(掌握) -->
  <button :class="{ active: isActive }" @click="btnClick">我是按钮</button>

  <!-- 2.2.对象语法的多个键值对 -->
  <button :class="{ active: isActive, why: true, kobe: false }" @click="btnClick">我是按钮</button>

  <!-- 2.3.动态绑定的class是可以和普通的class同时的使用 -->
  <button class="abc cba" :class="{ active: isActive, why: true, kobe: false }" @click="btnClick">我是按钮</button>

  <!-- 2.4.动态绑定的class是可以和普通的class同时的使用 -->
  <button class="abc cba" :class="getDynamicClasses()" @click="btnClick">我是按钮</button>

  <!-- 3.动态class可以写数组语法(了解) -->
  <h2 :class="['abc', 'cba']">Hello Array</h2>
  <h2 :class="['abc', className]">Hello Array</h2>
  <h2 :class="['abc', className, isActive? 'active': '']">Hello Array</h2>
  <h2 :class="['abc', className, { active: isActive }]">Hello Array</h2>
</div>
  
```

js

```javascript
const app = Vue.createApp({
  // data: option api
  data: function() {
    return {
      classes: "abc cba nba",
      isActive: false,
      className: "why"
    }
  },

  methods: {
    btnClick: function() {
      this.isActive = !this.isActive
    },

    getDynamicClasses: function() {
      return { active: this.isActive, why: true, kobe: false }
    }
  }
})

// 挂载app
app.mount("#app")
```



### 4.2. 绑定class – 数组语法

◼ 数组语法：我们可以把一个数组传给 :class，以应用一个 class 列表；

```html
<h2 :class="['abc', 'cba']">Hello Array</h2>
<h2 :class="['abc', className]">Hello Array</h2>
<h2 :class="['abc', className, isActive? 'active': '']">Hello Array</h2>
<h2 :class="['abc', className, { active: isActive }]">Hello Array</h2>
```





### 4.3. 绑定style -- 对象语法：

CSS property 名可以用驼峰式 (camelCase) 或短横线分隔 (kebab-case，记得用引号括起来) 来命名；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685547815118-bc5d5980-57a2-46ef-a47a-515dfafde98a.png)

```html
<div id="app">
  <!-- 1.普通的html写法 -->
  <h2 style="color: red; font-size: 30px;">哈哈哈哈</h2>

  <!-- 2.style中的某些值, 来自data中 -->
  <!-- 2.1.动态绑定style, 在后面跟上 对象类型 (重要)-->
  <h2 v-bind:style="{ color: fontColor, fontSize: fontSize + 'px' }">哈哈哈哈</h2>
  <!-- 2.2.动态的绑定属性, 这个属性是一个对象 -->
  <h2 :style="objStyle">呵呵呵呵</h2>

  <!-- 3.style的数组语法 -->
  <h2 :style="[objStyle, { backgroundColor: 'purple' }]">嘿嘿嘿嘿</h2>
</div>
```

### 4.4. 绑定style -- 数组语法：

*  :style 的数组语法可以将多个样式对象应用到同一个元素上

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685547839147-b9cbd3fe-b7da-4c87-9f12-3b57e61e2f3a.png)

### 4.5. 动态绑定属性 (了解)

◼ 在某些情况下，我们属性的名称可能也不是固定的：

*  前端我们无论绑定src、href、class、style，属性名称都是固定的；
*  如果属性名称不是固定的，我们可以使用 :[属性名]=“值” 的格式来定义；
*  这种绑定的方式，我们称之为动态绑定属性

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685549768014-6a877350-79f1-4e75-8070-93c52687c826.png)

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307091253630.png)



### 4.6. 绑定一个对象

◼ 如果我们希望将一个对象的所有属性，绑定到元素上的所有属性，应该怎么做呢？

*  非常简单，我们可以直接使用 v-bind 绑定一个 对象；

◼ 案例：info对象会被拆解成div的各个属性

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685549872764-5b4755dd-c226-44b3-a0af-295ed075b195.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685549879619-1163b793-a01d-49fa-ad6b-800456c7e144.png)



## 5. v-on绑定事件

◼ 前面我们绑定了元素的内容和属性，在前端开发中另外一个非常重要的特性就是交互。

◼ 在前端开发中，我们需要经常和用户进行各种各样的交互：

*  这个时候，我们就必须监听用户发生的事件，比如点击、拖拽、键盘事件等等
*  在Vue中如何监听事件呢？使用v-on指令。

◼ 接下来我们来看一下v-on的用法



### 5.1. v-on的用法

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685548103897-263a9caf-10ae-4aae-862b-a416dde315b2.png)



### 5.2. v-on的基本使用

◼ 我们可以使用v-on来监听一下点击的事件：

◼ v-on:click可以写成@click，是它的语法糖写法：

◼ 当然，我们也可以绑定其他的事件

◼ 如果我们希望一个元素绑定多个事件，这个时候可以传入一个对象

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685549976039-d2cf6e89-c149-4d9a-adc2-29a7b9a0455a.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685550061840-72e90ef5-2202-4c7a-8e4c-187ad0e104c4.png)



### 5.3. v-on参数传递

◼ 当通过methods中定义方法，以供@click调用时，需要注意参数问题：

◼ 情况一：如果该方法不需要额外参数，那么方法后的()可以不添加。

*  但是注意：如果方法本身中有一个参数，那么会默认将原生事件event参数传递进去

◼ 情况二：如果需要同时传入某个参数，同时需要event时，可以通过`$event`传入事件。

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685550108712-f20cc3dd-186a-4aba-a372-7d859b95a38e.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685550141087-89060d18-5571-46c4-97c9-dd309890befd.png)





### 5.4. v-on的修饰符

◼ v-on支持修饰符，修饰符相当于对事件进行了一些特殊的处理：

*  .stop - 调用 event.stopPropagation()。
*  .prevent - 调用 event.preventDefault()。
*  .capture - 添加事件侦听器时使用 capture 模式。
*  .self - 只当事件是从侦听器绑定的元素本身触发时才触发回调。
*  .{keyAlias} - 仅当事件是从特定键触发时才触发回调。
*  .once - 只触发一次回调。
*  .left - 只当点击鼠标左键时触发。
*  .right - 只当点击鼠标右键时触发。
*  .middle - 只当点击鼠标中键时触发。
*  .passive - { passive: true } 模式添加侦听器

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685549110171-12e4bbd1-9359-4ef5-a5db-477bac67cb29.png)





## 6. Vue的条件渲染

◼ 在某些情况下，我们需要根据当前的条件**决定某些元素或组件是否渲染**，这个时候我们就需要进行条件判断了。

◼ Vue提供了下面的指令来进行条件判断：

*  `v-if`
* `v-else`
*  `v-else-if`
*  `v-show`



### 6.1. v-if、v-else、v-else-if

◼ v-if、v-else、v-else-if用于根据条件来渲染某一块的内容：

*  这些内容只有在条件为true时，才会被渲染出来；
*  这三个指令与JavaScript的条件语句if、else、else if类似；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685550499952-7cef3406-ab95-4627-ac9c-639010a3decb.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685550516888-def9db67-a862-4ea8-a9e8-8a579e4f2cac.png)



◼ `v-if`的渲染原理：

*  `v-if`是惰性的；
*  当条件为`false`时，其判断的内容完全不会被渲染或者会被销毁掉；
*  当条件为`true`时，才会真正渲染条件块中的内容；



### 6.2. template元素

◼ 因为v-if是一个指令，所以必须将其添加到一个元素上：

*  但是如果我们希望切换的是多个元素呢？
*  此时我们渲染div，但是我们并不希望div这种元素被渲染；
*  这个时候，我们可以选择使用template；

◼ template元素可以当做不可见的包裹元素，并且在v-if上使用，但是最终template不会被渲染出来：

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685550557474-c80a532a-31d6-4fe0-b4a4-d4d1104cd59a.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685550590220-81cf17f0-662b-4388-bcda-e4985ec86e4c.png)



### 6.3. v-show

◼ v-show和v-if的用法看起来是一致的，也是根据一个条件决定是否显示元素或者组件

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685549388904-91233164-08ba-4fb4-b3bd-58342bcba8cf.png)



### 6.4. v-show和v-if的区别

◼ 首先，在用法上的区别：

*  v-show是不支持template；
*  v-show不可以和v-else一起使用；

◼ 其次，**本质的区别**：

*  `v-show`元素无论是否需要显示到浏览器上，它的DOM实际都是有存在的，只是通过**CSS的`display`属性来进行切换；**
*  `v-if`当条件为`false`时，其对应的原生压根不会被渲染到DOM中；

◼ 开发中如何进行选择呢？

*  如果我们的原生需要在显示和隐藏之间频繁的切换，那么使用v-show；
*  如果不会频繁的发生切换，那么使用v-if；