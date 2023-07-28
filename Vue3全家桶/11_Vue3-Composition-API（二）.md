## 1. computed函数使用

◼ 计算属性`computed`：当我们的某些属性是依赖其他状态时，我们可以使用计算属性来处理

* 在`Options API`中，我们是使用`computed`选项来完成的；
* 在`Composition API`中，我们可以在 `setup` 函数中使用 `computed` 方法来编写一个计算属性；

◼ 如何使用`computed`呢？

* 方式一：接收一个`getter`**函数**，并为 `getter` 函数返回的值，返回一个**不变**的 `ref` 对象；
* 方式二：接收一个具有 `get` 和 `set `的**对象**，返回一个**可变**的（可读写）`ref` 对象；

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282039750.png)![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282039764.png)

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282039735.png)



## 2. setup中使用ref

◼ 在setup中如何使用`ref`获取元素或者组件？

* 其实非常简单，我们只需要定义一个`ref`对象，绑定到元素或者组件的`ref`属性上即可；

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282039794.png)![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282039810.png)

## 3. 组件的生命周期函数

◼  `setup` 可以用来替代 `data` 、 `methods` 、 `computed` 等等这些选项，也可以替代生命周期钩子。

◼ 那么`setup`中如何使用生命周期函数呢？

可以使用直接导入的 `onX` 函数注册生命周期钩子；

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282039858.png)![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282039287.png)



为什么没有`created` 以及 `beforeCreate` ? 

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282039366.png)



## 4. Provide/Inject使用

### 4.1. Provide函数

◼ 事实上`Composition API`也可以替代之前的 `Provide` 和 `Inject` 的选项。

◼ 可以通过 `provide`来提供数据：

* 可以通过 `provide` 方法来定义每个 `Property`；

◼ `provide`可以传入两个参数：

* `name`：提供的属性名称；
* `value`：提供的属性值；

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282039410.png)





### 4.2. Inject函数

◼ 在 后代组件 中可以通过 `inject` 来注入需要的属性和对应的值：

* 可以通过 `inject` 来注入需要的内容；

◼ `inject`可以传入两个参数：

1. 需要 `inject` 属性的 `name`；
2. 默认值；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1686499601457-890c06e7-3fd3-4182-832f-4121417de947.png)



### 4.3. 数据的响应式

◼ 为了增加 provide 值和 inject 值之间的响应性，我们可以在 provide 值时使用 ref 和 reactive。

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282039596.png)



## 5. watch/watchEffect

### 5.1. 侦听数据的变化

◼ 在前面的`Options API`中，我们可以通过`watch`选项来侦听`data`或者`props`的数据变化，当数据变化时执行某一些操作。

◼ 在`Composition API`中，我们可以使用`watchEffect`和`watch`来完成响应式数据的侦听；

* `watchEffect`：用于自动收集响应式数据的依赖；
* `watch`：需要手动指定侦听的数据源；

### 5.2. Watch的使用

◼ `watch`的`API`完全等同于组件`watch`选项的`Property`：

* `watch`需要侦听**特定的数据源，并且执行其回调函数；**
* 默认情况下它是惰性的，只有当被侦听的源发生变化时才会执行回调；

```
 watch(counter, (newValue, oldValue) => {})
```

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282039775.png)





### 5.3. 侦听多个数据源

◼ 侦听器还可以使用数组同时侦听多个源：

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282039857.png)

### 5.4. watch的选项

◼ 如果我们希望侦听一个深层的侦听，那么依然需要设置 deep 为true：

* 也可以传入 immediate 立即执行；

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282039840.png)



### 5.5. watchEffect

◼ 当侦听到某些响应式数据变化时，我们希望执行某些操作，这个时候可以使用 `watchEffect`。

◼ 我们来看一个案例：

*  首先，watchEffect传入的函数会被立即执行一次，并且在执行的过程中会**收集依赖**；
*  其次，**只有收集的依赖发生变化时，watchEffect传入的函数才会再次执行；**

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282039932.png)



### 5.6. watchEffect的停止侦听

◼ 如果在发生某些情况下，我们希望停止侦听，这个时候我们可以获取watchEffect的返回值函数，调用该函数即可。

◼ 比如在上面的案例中，我们age达到20的时候就停止侦听：

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282039174.png)

## 6. 自定义Hook练习

### 6.1. useCounter

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282039291.png)

### 6.2. useTitle

我们编写一个修改title的Hook：

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282039308.png)



### 6.3. useScrollPosition

◼ 我们来完成一个监听界面滚动位置的Hook：

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282039367.png)





## 7. script setup语法糖

### 7.1. 如何使用

◼ `<script setup>` 是在单文件组件 (`SFC`) 中使用组合式 `API` 的编译时语法糖，当同时使用 `SFC` 与组合式 `API `时则推荐该语法。

* 更少的样板内容，更简洁的代码；
* 能够使用纯 `Typescript` 声明 `prop` 和抛出事件；
* 更好的运行时性能 ；
* 更好的 `IDE` 类型推断性能 ；

◼ 使用这个语法，需要将 `setup attribute` 添加到 `<script>` 代码块上：

```javascript
<script setup>
  console.log("Hello world");
</script>
```

◼ 里面的代码会被编译成组件 `setup()` 函数的内容：

* 这意味着与普通的 `<script> `只在组件被首次引入的时候执行一次不同；
* `<script setup>` 中的代码**会在每次组件实例被创建的时候执行。**



### 7.2. 顶层的绑定会被暴露给模板

◼ 当使用 `<script setup>` 的时候，任何在 `<script setup>` 声明的顶层的绑定 (包括变量，函数声明，以及 `import` 引入的内容) 都能在模板中直接使用：

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282039444.png)

◼ 响应式数据需要通过`ref`、`reactive`来创建



### 7.3. 导入的组件直接使用

◼ `<script setup>` 范围里的值也能被直接作为自定义组件的标签名使用：

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282039654.png)



### 7.4. defineProps() 和 defineEmits()

◼ 为了在声明 `props` 和 `emits` 选项时获得完整的类型推断支持，我们可以使用 `defineProps` 和 `defineEmits` API，它们将自动地在 `<script setup>` 中可用：

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282039804.png)![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282039788.png)



### 7.5. defineExpose()

◼ 使用 `<script setup>` 的组件是**默认关闭**的：

* 通过模板 `ref`或者` $parent` 链获取到的组件的公开实例，不会暴露任何在 `<script setup>` 中声明的绑定；

◼ 通过 `defineExpose` 编译器宏来显式指定在 `<script setup>` 组件中要暴露出去的 `property`(属性)

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282039845.png)![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282039964.png)



### 7.6. 案例实战练习

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282039255.png)

目录文件夹

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282039303.png)





main.js 文件

```javascript
import { createApp } from 'vue'
import App from './App.vue'
import "./assets/css/reset.css"

createApp(App).mount('#app')
```



App.vue 文件

```vue
<template>
  <div class="app">
    <!-- 1.高评价 -->
    <room-area :area-data="highScore"></room-area>
  </div>
</template>

<script setup>
  import { ref } from 'vue';
  import RoomArea from './components/RoomArea.vue'

  // 1.获取数据
  // import highScore from "./data/high_score.json"
  // console.log(highScore)


  // 2.模拟网络请求数据
  const highScore = ref({})
  setTimeout(() => {
    import("./data/high_score.json").then(res => {
      highScore.value = res.default
    })
  }, 1000);

</script>

<style lang="less" scoped>
  .app {
    width: 1032px;
    padding: 40px;
    margin: 0 auto;
  }
</style>
```



AreaHeader.vue 文件

```vue
<template>
  <div class="area-header">
    <h3 class="title">{{ title }}</h3>
    <div class="subtitle">{{ subtitle }}</div>
  </div>
</template>

<script setup>

defineProps({
  title: {
    type: String,
    default: "默认标题"
  },
  subtitle: {
    type: String,
    default: "默认子标题"
  }
})

</script>

<style lang="less" scoped>

  .area-header {
    height: 84px;

    .title {
      font-size: 22px;
    }

    .subtitle {
      margin-top: 12px;
      font-size: 16px;
    }
  }

</style>
```





RoomArea.vue 文件

```vue
<template>
  <div class="room-area">
    <!-- 1.区域header -->
    <area-header :title="areaData.title" :subtitle="areaData.subtitle"/>

    <!-- 2.房间列表 -->
    <div class="room-list">
      <template v-for="item in areaData.list" :key="item.id">
        <room-item :item-data="item"/>
      </template>
    </div>
  </div>
</template>

<script setup>
import AreaHeader from './AreaHeader.vue'
import RoomItem from './RoomItem.vue'

defineProps({
  areaData: {
    type: Object,
    default: () => ({})
  }
})

</script>

<style lang="less" scoped>
  .room-list {
    display: flex;
    flex-wrap: wrap;
    margin: 20px -8px;
  }
</style>
```



RoomItem.vue 文件

```vue
<template>
  <div class="room-item">
    <div class="item-inner">
      <div class="cover">
        <img :src="itemData.picture_url" alt="">
      </div>
      <div class="info">
        <div class="title" :style="{ color: titleInfo.color }">
          {{ titleInfo.text }}
        </div>
        <div class="name">
          {{ itemData.name }}
        </div>
        <div class="price">
          {{ itemData.price_format + "/晚" }}
        </div>
        <div class="bottom-info" :style="bottomInfo.style">
          {{ bottomInfo.content }}
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
  import { computed } from 'vue';

  // 1.定义props
  const props = defineProps({
    itemData: {
      type: Object,
      default: () => ({})
    }
  })

  // 2.定义计算属性
  // const titleText = computed(() => {
  //   return props.itemData.verify_info.messages.join(" ")
  // })
  // const titleColor = computed(() => {
  //   return props.itemData.verify_info.text_color
  // })
  const titleInfo = computed(() => {
    return {
      text: props.itemData.verify_info.messages.join(" "),
      color: props.itemData.verify_info.text_color
    }
  })
  const bottomInfo = computed(() => {
    return {
      content: props.itemData.bottom_info.content,
      style: {
        color: props.itemData.bottom_info.content_color,
        fontSize: props.itemData.bottom_info.font_size + "px"
      }
    }
  })

</script>

<style lang="less" scoped>
  .room-item {
    width: 33.333333%;

    .item-inner {
      margin: 0 8px 12px;
      color: #484848;
      font-weight: 800;

      img {
        width: 100%;
        border-radius: 3px;
      }

      .info {
        .title {
          margin-top: 8px;
          font-size: 12px;
        }

        .name {
          margin-top: 3px;
          font-size: 16px;

          overflow: hidden;
          text-overflow: ellipsis;
          display: -webkit-box;
          -webkit-line-clamp: 2;
          -webkit-box-orient: vertical;
        }

        .price {
          margin: 3px 0;
          font-size: 14px;
          font-weight: 400;
        }
      }
    }
  }
</style>
```





json大概这样子

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307282039318.png)