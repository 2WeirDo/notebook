## 1. Vue引入方式

### 1.1. 方式一：CDN引入

```javascript
<script scr = "https://unpkg.com/vue@3/dist/vue.global.js"></script>
```

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685256261672-2848ce85-7c9a-4782-b89e-e7dd9d353989.png)

### 1.2. 方式二：下载和引入

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685256287301-cce79c98-6338-4801-b787-e0d4eefa1ab9.png)

## 2. Vue案例初体验

### 2.1. 动态展示数据

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685256318469-665f2156-14e0-49e0-abdc-6ddc5e47ba47.png)



### 2.2. 展示列表的数据

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685256383406-6300ae27-d22c-468d-b193-9c366f01d8aa.png)





### 2.3. 计数器功能实现

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685256473858-63637d23-ca9b-4631-b2d7-9c8e0f7e1cd6.png)



优化

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685256577414-98ed2e00-5dff-48fa-b6f0-cc85c5839302.png)

## 3. 命令和声明式编程

### 3.1. 他们是什么

◼ 原生开发和Vue开发的模式和特点，我们会发现是完全不同的，这里其实涉及到两种不同的编程范式：

-  命令式编程和声明式编程；
-  **命令式**编程关注的是 “**how to do**”自己完成整个how的过程；
-  **声明式**编程关注的是 “**what to do**”，**由框架(机器)完成 “how”**的过程；

◼ 在原生的实现过程中，我们是如何操作的呢？

-  我们每完成一个操作，都需要通过JavaScript编写一条代码，来给浏览器一个指令；
-  这样的编写代码的过程，我们称之为命令式编程；
-  在早期的原生JavaScript和jQuery开发的过程中，我们都是通过这种命令式的方式在编写代码的；

◼ 在Vue的实现过程中，我们是如何操作的呢？

-  我们会在createApp传入的对象中声明需要的内容，模板template、数据data、方法methods；
-  这样的编写代码的过程，我们称之为是声明式编程；
-  目前Vue、React、Angular、小程序的编程模式，我们称之为声明式编程；



### 3.2. MVVM模型

◼ MVC和MVVM都是一种软件的体系结构

-  `MVC`是Model – View –Controller的简称，是在前期被使用非常框架的架构模式，比如iOS、前端；
-  `MVVM`是Model-View-ViewModel的简称，是目前非常流行的架构模式；

◼ 通常情况下，我们也经常称Vue是一个MVVM的框架。

-  Vue官方其实有说明，Vue虽然并没有完全遵守MVVM的模型，但是整个设计是受到它的启发的

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685256751590-b50bdacc-9f08-4119-bfa2-b5ef9d06d84a.png)

## 4. Vue的options

### 4.1. data属性

◼ data属性是传入一个**函数**，并且该函数需要返回一个**对象**：

-  在Vue2.x的时候，也可以传入一个对象（虽然官方推荐是一个函数）；
-  在Vue3.x的时候，必须传入一个函数，否则就会直接在浏览器中报错；

◼ data中返回的对象会被Vue的响应式系统劫持，**之后对该对象的修改或者访问都会在劫持中被处理：**

-  所以我们在template或者app中通过 {{counter}} 访问counter，可以从对象中获取到数据；
-  所以我们修改counter的值时，app中的 {{counter}}也会发生改变；



### 4.2. methods属性

◼ methods属性是一个**对象**，通常我们会在这个对象中定义很多的方法：

-  这些方法可以被绑定到模板中；
-  在该方法中，我们可以使用this关键字来直接访问到data中返回的对象的属性；

◼ 对于有经验的同学，在这里我提一个问题，官方文档有这么一段描述：

-  问题一：为什么不能使用箭头函数（官方文档有给出解释）？

箭头函数this指向外层作用域, 这里如果用箭头函数不会指向data中的数据

-  问题二：不使用箭头函数的情况下，this到底指向的是什么？（可以作为一道面试题）

指向data返回的对象

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685257082150-ebd5998f-2956-480a-992a-57bb448ad9aa.png)