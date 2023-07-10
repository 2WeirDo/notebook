## 1. 复杂data的处理方式

◼ 我们知道，在模板中可以直接通过插值语法显示一些data中的数据。

◼ 但是在某些情况，我们可能需要对数据进行一些转化后再显示，或者需要将多个数据结合起来进行显示；

*  比如我们需要对多个data数据进行运算、三元运算符来决定结果、数据进行某种转化后显示；
*  在模板中使用表达式，可以非常方便的实现，但是设计它们的初衷是用于简单的运算；
*  在模板中放入太多的逻辑会让模板过重和难以维护；
*  并且如果多个地方都使用到，那么会有大量重复的代码；

◼ 我们有没有什么方法可以将逻辑抽离出去呢？

*  可以，其中一种方式就是将逻辑抽取到一个method中，放到methods的options中；
*  但是，这种做法有一个直观的弊端，就是所有的data使用过程都会变成了一个方法的调用；
*  **另外一种方式就是使用计算属性computed；**



## 2. computed计算属性使用

### 2.1. 认识计算属性computed

◼ 什么是计算属性呢？

*  官方并没有给出直接的概念解释；
*  而是说：对于任何包含响应式数据的复杂逻辑，你都应该使用计算属性；
*  计算属性将被混入到组件实例中

◼ 计算属性的用法：

*  选项：`computed`
*  类型：`{ [key: string]: Function | { get: Function, set: Function } }`

◼ 那接下来我们通过案例来理解一下这个计算属性。

### 2.2. 案例实现思路

◼ 我们来看三个案例：

◼ 案例一：我们有两个变量：firstName和lastName，希望它们拼接之后在界面上显示；

◼ 案例二：我们有一个分数：score

*  当score大于60的时候，在界面上显示及格；
*  当score小于60的时候，在界面上显示不及格；

◼ 案例三：我们有一个变量message，记录一段文字：比如Hello World

*  某些情况下我们是直接显示这段文字；
*  某些情况下我们需要对这段文字进行**反转**；

◼ 我们可以有三种实现思路：

*  思路一：在模板语法中直接使用表达式；
*  思路二：使用method对逻辑进行抽取；
*  思路三：使用计算属性computed；

#### 2.2.1. 实现思路一：模板语法

◼ 思路一的实现：模板语法

*  缺点一：模板中存在大量的复杂逻辑，不便于维护（模板中表达式的初衷是用于简单的计算）；
*  缺点二：当有多次一样的逻辑时，存在重复的代码；
*  缺点三：多次使用的时候，很多运算也需要多次执行，没有缓存；

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307102321025.png)

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307102321144.png)



#### 2.2.2. 实现思路二：method实现

◼ 思路二的实现：method实现

*  缺点一：我们事实上先显示的是一个结果，但是都变成了一种方法的调用；
*  缺点二：多次使用方法的时候，没有缓存，也需要多次计算；

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307102321204.png)

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307102321129.png)





#### 2.2.3. 思路三的实现：computed实现

◼ 思路三的实现：computed实现

*  注意：计算属性看起来像是一个函数，但是我们在使用的时候不需要加()，这个后面讲setter和getter时会讲到；
*  我们会发现无论是直观上，还是效果上计算属性都是更好的选择；
*  并且计算属性是**有缓存的**；

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307102321187.png)

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307102321149.png)

## 3. computed和method区别

### 3.1. 它们的可见不同

◼ 在上面的实现思路中，我们会发现计算属性和methods的实现看起来是差别是不大的，而且我们多次提到计算属性有缓存的。

◼ 接下来我们来看一下同一个计算多次使用，计算属性和methods的差异：

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307102321207.png)

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307102321343.png)

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307102321403.png)



### 3.2. 计算属性的缓存

◼ 这是什么原因呢？

*  这是因为计算属性会基于它们的依赖关系进行缓存；
*  在数据不发生变化时，计算属性是不需要重新计算的；
*  但是如果依赖的数据发生变化，在使用时，计算属性依然会重新进行计算；

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307102321423.png)

## 4. computed的set和get

### 4.1. set和get用法

◼ 计算属性在大多数情况下，只需要一个getter方法即可，所以我们会将计算属性直接写成一个函数。

◼ 但是，如果我们确实想设置计算属性的值呢？(用的少)

*  这个时候我们也可以给计算属性设置一个setter的方法；

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307102321409.png)

### 4.2. 源码如何对setter和getter处理呢？（了解）

◼ 你可能觉得很奇怪，Vue内部是如何对我们传入的是一个getter，还是说是一个包含setter和getter的对象进行处理的呢？

*  事实上非常的简单，Vue源码内部只是做了一个逻辑判断而已；

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307102321517.png)





## 5. 侦听器watch选项使用

### 5.1. 认识侦听器watch

◼ 什么是侦听器呢？

*  开发中我们在data返回的对象中定义了数据，这个数据通过插值语法等方式绑定到template中；
*  当数据变化时，template会自动进行更新来显示最新的数据；
*  但是在某些情况下，我们希望在代码逻辑中监听某个数据的变化，这个时候就需要用侦听器watch来完成了；

◼ 侦听器的用法如下：

*  选项：`watch`
*  类型：`{ [key: string]: string | Function | Object | Array}`

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307102321444.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685553951575-183c41fa-1603-418d-ae3d-aecd1844d940.png)



### 5.2. 侦听器案例

◼ 举个栗子（例子）：

*  比如现在我们希望用户在input中输入一个问题；
*  每当用户输入了最新的内容，我们就获取到最新的内容，并且使用该问题去服务器查询答案；
*  那么，我们就需要实时的去获取最新的数据变化；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685552883645-f90d596d-2ab8-4901-ba45-9724e8140500.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685552890387-122cc037-91a8-4e52-8ecb-f49fcf4a2a2f.png)

### 5.3. 侦听器watch的配置选项

◼ 我们先来看一个例子：

*  当我们点击按钮的时候会修改info.name的值；
*  这个时候我们使用watch来侦听info，可以侦听到吗？答案是不可以。

◼ 这是因为默认情况下，watch只是在侦听info的引用变化，对于内部属性的变化是不会做出响应的：

*  这个时候我们可以使用一个选项deep进行更深层的侦听；
*  注意前面我们说过watch里面侦听的属性对应的也可以是一个Object；

◼ 还有另外一个属性，是希望一开始的就会立即执行一次：

*  这个时候我们使用immediate选项；
*  这个时候无论后面数据是否有变化，侦听的函数都会有限执行一次

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685554115813-a785d724-b64a-48dc-b044-97e5822196e3.png)

## 6. 侦听器watch其他写法

### 6.1. 侦听器watch的其他方式（一）

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685552954249-b9f6d68f-24a3-4571-accc-fe62e5d4d2ae.png)

someMethod是一个方法

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685552959366-7c8d64a7-7847-407e-a2f2-b3b6b7eb0185.png)

handle1是一个方法

### 6.2. 侦听器watch的其他方式（二）

◼ 另外一个是Vue3文档中没有提到的，但是Vue2文档中有提到的是侦听对象的属性：

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685554184171-34fad4ac-c05c-4593-a311-f6369f78172d.png)

◼ 还有另外一种方式就是使用 $watch 的API：

◼ 我们可以在created的生命周期（后续会讲到）中，使用 this.$watchs 来侦听；

*  第一个参数是要侦听的源；
*  第二个参数是侦听的回调函数callback；
*  第三个参数是额外的其他选项，比如deep、immediate；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685554167506-d9222db8-d668-4f34-bc24-dbfc6793e1ca.png)

## 7. 阶段性综合案例练习

◼ 现在我们来做一个相对综合一点的练习：书籍购物车

◼ 案例说明：

*  1.在界面上以表格的形式，显示一些书籍的数据；
*  2.在底部显示书籍的总价格；
*  3.点击+或者-可以增加或减少书籍数量（如果为1，那么不能继续-）；
*  4.点击移除按钮，可以将书籍移除（当所有的书籍移除完毕时，显示：购物车为空~）；
*  5.点击哪一行，哪一行背景色变为浅蓝色；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685553182936-f3bb78bd-4e64-440d-bde1-1dd6ed3e8669.png)



完整代码如下

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <style>
    table {
      border-collapse: collapse;
    }
    .active {
      background-color: skyblue;
    }
    th,
    td {
      padding: 8px 16px;
      border: solid 1px;
      font-weight: 700;
    }
  </style>
  <body>
    <div id="app">
      <template v-if="books.length">
        <table>
          <thead>
            <tr>
              <th>序号</th>
              <th>书籍名称</th>
              <th>出版日期</th>
              <th>价格</th>
              <th>购买数量</th>
              <th>操作</th>
            </tr>
          </thead>
          <tbody>
            <tr
              @click="changeColor(index)"
              v-for="(item, index) in books"
              :key:item.id
              :class="{active : index == currentIndex}"
              >
              <td>{{index + 1}}</td>
              <td>{{item.name}}</td>
              <td>{{item.date}}</td>
              <td>￥{{item.price}}</td>
              <td>
                <button
                  :disabled="item.count < 1 ? true : false"
                  @click="sub(item)"
                  >
                  -
                </button>
                {{item.count}}
                <button @click="add(item)">+</button>
              </td>
              <td><button @click="removeItem(index)">移除</button></td>
            </tr>
          </tbody>
        </table>
        <h2>总价 {{ getSum }}</h2>
      </template>
      <template v-else>
        <div>
          <h2>瓦特 没书了</h2>
        </div>
      </template>
    </div>

    <script src="../lib/vue.js"></script>
    <script src="./data/data.js"></script>
    <script>
      const app = Vue.createApp({
        data() {
          return {
            books,
            currentIndex: 0,
          };
        },
        computed: {
          getSum() {
            return this.books.reduce((pre, item) => {
              return pre + item.price * item.count;
            }, 0);
          },
        },
        methods: {
          changeColor(index) {
            this.currentIndex = index;
          },
          sub(item) {
            item.count--;
          },
          add(item) {
            item.count++;
          },
          removeItem(index) {
            this.books.splice(index, 1);
          },
        },
      });

      app.mount("#app");
    </script>
  </body>
</html>
```