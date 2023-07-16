## 1. v-model基本使用



◼ **表单提交**是开发中非常常见的功能，也是和用户交互的重要手段：

* 比如用户在登录、注册时需要提交账号密码；
* 比如用户在检索、创建、更新信息时，需要提交一些数据；

◼ 这些都要求我们可以在代码逻辑中获取到用户提交的数据，我们通常会使用`v-model`指令来完成：

* `v-model`指令可以在表单 input、textarea以及select元素上创建双向数据绑定；
* 它会根据控件类型自动选取正确的方法来更新元素；
* 尽管有些神奇，但 `v-model` 本质上不过是语法糖，它负责监听用户的输入事件来更新数据，并在某种极端场景下进行一些特殊处理；

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307162021474.png)

## 2. v-model绑定原理

◼ 官方有说到，`v-model`的原理其实是背后有两个操作：

* `v-bind`绑定`value`属性的值；
* `v-on`绑定`input`事件监听到函数中，函数会获取最新的值赋值到绑定的属性中；

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307162021507.png)

手动实现双向绑定

```
<input type="text" :value="message" @input="message = $event.target.value">
```

v-model实现双向绑定

```
<input type="text" v-model="message">
```





## 3. v-model绑定textarea

◼ 我们再来绑定一下其他的表单类型：textarea、checkbox、radio、select

◼ 我们来看一下**绑定**`textarea`：

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307162021489.png)



## 4. v-model绑定radio

◼ v-model绑定radio，用于选择其中一项；

注意, value 得取不同值 , 用于排斥, 因为是单选框, 只能选一个

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307162021514.png)





## 5. v-model绑定checkbox

* `checkbox`单选框 :  绑定到属性中的值是一个`Boolean`
* `checkbox`多选框 :  绑定到属性中的值是一个数组 --

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307162021500.png)

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307162021494.png)



## 6. v-model绑定select

◼ 和checkbox一样，select也分单选和多选两种情况。

◼ 单选：只能选中一个值

* v-model绑定的是一个值；
* 当我们选中option中的一个时，会将它对应的value赋值到fruit中；

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307162021505.png)

◼ 多选：可以选中多个值

* v-model绑定的是一个数组；
* 当选中多个值时，就会将选中的option对应的value添加到数组fruit中；

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307162021500.png)





![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307162021510.png)![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307162021513.png)



## 7. v-model的值绑定

◼ 目前我们在前面的案例中大部分的值都是在template中固定好的：

* 比如gender的两个输入框值male、female；
* 比如hobbies的三个输入框值basketball、football、tennis；

◼ 在真实开发中，我们的数据可能是来自服务器的，那么我们就可以先将值请求下来，绑定到data返回的对象中，再通过`v-bind`来进行值的绑定，这个过程就是值绑定。

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307162021540.png)

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307162021534.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685846754378-6e5ec1b7-a28b-448f-bda0-692924dce8ed.png)

## 8. v-model的修饰符

### 8.1. lazy

◼ lazy修饰符是什么作用呢？

* 默认情况下，v-model在进行双向绑定时，绑定的是input事件，那么会在每次内容输入后就将最新的值和绑定的属性进行同步；
* 如果我们在v-model后跟上lazy修饰符，那么会将绑定的事件切换为 `change` 事件，只有在提交时（比如回车）才会触发；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685845253225-24fff276-dcc0-41b1-9c07-ae4dc364f1d3.png)



### 8.2. number

◼ 我们先来看一下`v-model`绑定后的值是什么类型的：

* message总是string类型，即使在我们设置type为number也是string类型；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685845275742-8de5ca4a-c47c-4482-a690-e22e6a15f4c1.png)

◼ 如果我们希望转换为数字类型，那么可以使用 .number 修饰符：

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685845288583-61ebc50d-1cae-4586-bb31-742fd566c578.png)

◼ 另外，在我们进行逻辑判断时，如果是一个string类型，在可以转化的情况下会进行隐式转换的：

* 下面的score在进行判断的过程中会进行隐式转化的；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1685845302687-2e50b183-9400-4158-9d2c-c4b3e813004f.png)



### 8.3. trim

◼ 如果要自动过滤用户输入的守卫空白字符，可以给`v-model`添加 `trim` 修饰符：

![img](https://weirdo-blog.oss-cn-chengdu.aliyuncs.com/blog/202307162021079.png)