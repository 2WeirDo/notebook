## 1. v-for列表渲染

◼ `v-for`的基本格式是 "`item in array`"：

*  数组通常是来自data或者prop，也可以是其他方式；
*  item是我们给每项元素起的一个别名，这个别名可以自定来定义；

◼ 我们知道，在遍历一个数组的时候会经常需要拿到数组的索引：

*  如果我们需要索引，可以使用格式： "`(item, index) in array`"；
*  注意上面的顺序：数组元素项`item`是在前面的，索引项`index`是在后面的；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685551756675-a44a7d88-4dae-459f-ba83-fa668a10d542.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685551769423-f9286d6c-38f1-42a4-8728-83248da7bcc6.png)



## 2. v-for渲染类型

### 2.1. v-for支持的类型

◼ v-for也支持遍历对象，并且支持有一二三个参数：

*  一个参数： `"value in object"`;
*  二个参数： `"(value, key) in object"`;
*  三个参数： `"(value, key, index) in object"`;

◼ v-for同时也支持数字的遍历：

*  每一个item都是一个数字； 
* 生成从 1 开始到 number 的数字

◼ v-for也可以遍历其他可迭代对象(Iterable)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685551823096-60fc3313-67e8-4fb8-8678-f4307383f2a1.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685551851842-5efe0610-19ff-4b62-a929-e858a216743d.png)



### 2.2. template元素

◼ 类似于v-if，你可以使用 template 元素来循环渲染一段包含多个元素的内容：

*  我们使用template来对多个元素进行包裹，而不是使用div来完成；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685551938591-c6297eca-2ae0-47d7-9a69-5afead3cfb79.png)

## 

## 3. 数组更新的检测

◼ Vue 将被侦听的数组的变更方法进行了包裹，所以它们也将会触发视图更新。

◼ 这些被包裹过的方法包括：

*  `push()`
*  `pop()`
*  `shift()`
*  `unshift()`
* ` splice()`
*  `sort()`
*  `reverse()`

◼ 替换数组的方法

*  上面的方法会直接修改原来的数组；
*  但是某些方法不会替换原来的数组，而是会生成新的数组，比如 `filter()`、`concat()` 和 `slice()`；这时候需要重新赋值

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685552010122-b91181e9-7d9b-4876-9436-451f3a969498.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685552030382-783cde0f-267d-4d60-8894-cf94b8488e54.png)

## 4. v-for的key属性

### 4.1. v-for中的key是什么作用？

◼ 在使用v-for进行列表渲染时，我们通常会给元素或者组件绑定一个key属性。

◼ 这个key属性有什么作用呢？我们先来看一下官方的解释：

*  key属性主要用在Vue的虚拟DOM算法，在新旧nodes对比时辨识VNodes；
*  如果不使用key，Vue会使用一种最大限度减少动态元素并且尽可能的尝试就地修改/复用相同类型元素的算法；
*  而使用key时，它会基于key的变化重新排列元素顺序，并且会移除/销毁key不存在的元素；

◼ 官方的解释对于初学者来说并不好理解，比如下面的问题：

*  什么是新旧nodes，什么是VNode？
*  没有key的时候，如何尝试修改和复用的？
*  有key的时候，如何基于key重新排列的？

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685552084024-edeeaabe-9d86-4c17-b416-1e1886043c59.png)

### 4.2. 认识VNode

◼ 我们先来解释一下VNode的概念：

*  因为目前我们还没有比较完整的学习组件的概念，所以目前我们先理解HTML元素创建出来的VNode；
*  VNode的全称是`Virtual Node`，也就是虚拟节点；
*  事实上，无论是组件还是元素，它们最终在Vue中表示出来的都是一个个VNode；
*  VNode的本质是一个JavaScript的对象；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685551251799-7ecab3cd-12b5-4820-b54b-e2ea9b5bba68.png)

## 5. Vue的虚拟DOM

### 5.1. 认识虚拟DOM

◼ 如果我们不只是一个简单的div，而是有一大堆的元素，那么它们应该会形成一个VNode Tree：

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685551272484-badb90b6-3f35-4206-8dd9-b13ff7d06fee.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685551285147-ebcb1495-7be7-4a39-b2d2-2dec522f2b0f.png)

### 5.2. 插入F的案例

◼ 我们先来看一个案例：这个案例是当我点击按钮时会在中间插入一个f；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685551397103-e00e70cb-5ed3-4d17-bada-a7ab53e91097.png)

◼ 我们可以确定的是，这次更新对于ul和button是不需要进行更新，需要更新的是我们li的列表：

* 在Vue中，对于相同父元素的子元素节点并不会重新渲染整个列表；
* 因为对于列表中 a、b、c、d它们都是没有变化的；
* 在操作真实DOM的时候，我们只需要在中间插入一个f的li即可；

◼ 那么Vue中对于列表的更新究竟是如何操作的呢？

* Vue事实上会对于有key和没有key会调用两个不同的方法；
* 有key，那么就使用 patchKeyedChildren方法；
* 没有key，那么久使用 patchUnkeyedChildren方法；



## 6. v-for的diff算法

### 6.1. 没有key的过程如下

◼ 我们会发现下面的diff算法效率并不高：

*  c和d来说它们事实上并不需要有任何的改动；
*  但是因为我们的c被f所使用了，所有后续所有的内容都要一次进行改动，并且最后进行新增；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685551552229-b2d03c8b-7897-4a5c-a25d-ada455063662.png)

### 6.2. 有key的diff算法如下（一）

◼ 第一步的操作是从头开始进行遍历、比较：

*  a和b是一致的会继续进行比较；
*  c和f因为key不一致，所以就会break跳出循环；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685551536250-d4752508-94bb-46da-a42e-4c7572541284.png)

◼ 第二步的操作是从尾部开始进行遍历、比较

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685551564377-e825706d-eac7-49f5-8b71-1b22f9cab8bc.png)

### 6.3. 有key的diff算法如下（二）

◼ 第三步是如果旧节点遍历完毕，但是依然有新的节点，那么就新增节点：

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685551589157-14ac3d37-a2d3-4200-81c7-714f1b8d4835.png)

◼ 第四步是如果新的节点遍历完毕，但是依然有旧的节点，那么就移除旧节点：

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685551600946-3f2146dc-b246-4aa2-a9f8-f1876fae1e19.png)



### 6.4. 有key的diff算法如下（三）

◼ 第五步是最特色的情况，中间还有很多未知的或者乱序的节点：

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685551618937-e689b027-b315-458e-8475-edeb617a0908.png)

◼ 所以我们可以发现，Vue在进行diff算法的时候，会尽量利用我们的key来进行优化操作：

*  在没有key的时候我们的效率是非常低效的；
*  在进行插入或者重置顺序的时候，**保持相同的key可以让diff算法更加的高效；**

## 