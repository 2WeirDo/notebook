## 1. 监听对象的操作



◼ 我们先来看一个需求：有一个对象，我们希望监听这个对象中的属性被设置或获取的过程

- 通过我们前面所学的知识，能不能做到这一点呢？
-  其实是可以的，我们可以通过之前的属性描述符中的存储属性描述符来做到；

◼ 下面这段代码就利用了前面讲过的 `Object.defineProperty` 的存储属性描述符来对属性的操作进行监听。

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682092499369-a91070f4-723d-4166-9087-d43a61774d74.png)

### 1.1. Object.defineProperty监听对象的缺点

◼ 但是这样做有什么缺点呢？

*  首先，`Object.defineProperty`设计的初衷，不是为了去监听截止一个对象中所有的属性的。

我们在定义某些属性的时候，初衷其实是定义普通的属性，但是后面我们强行将它变成了数据属性描述符。其次，如果我们想监听更加丰富的操作，比如新增属性、删除属性，那么Object.defineProperty是无能为力的。

在上面使用`Object.defineProperty`的时候，我们遇到的问题有  : 

1. 一次只能对一个属性进行监听，需要遍历来对所有属性监听。
2. 在遇到一个对象的属性还是一个对象的情况下，需要递归监听。
3. 对于对象的新增属性，需要手动监听
4. 对于数组通过`push`、`unshift`方法增加的元素，也无法监听

# 

## 1. Proxy

### 1.1. Proxy基本使用

◼ 在`ES6`中，新增了一个`Proxy`类，这个类从名字就可以看出来，是用于帮助我们创建一个代理的：

- 也就是说，如果我们希望监听一个对象的相关操作，那么我们可以先创建一个代理对象（`Proxy`对象）；
- 之后对该对象的所有操作，都通过代理对象来完成，代理对象可以监听我们想要对原对象进行哪些操作；

◼ 我们可以将上面的案例用`Proxy`来实现一次：

- 首先，我们需要`new Proxy`对象，并且传入需要侦听的对象以及一个处理对象，可以称之为`handler`；
- `const p = new Proxy(target, handler)`
- 其次，我们之后的操作都是直接对`Proxy`的操作，而不是原有的对象，因为我们需要在`handler`里面进行侦听；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682092943690-b1f81e4e-910e-45ba-a45c-969253ba7573.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682093736419-7fc70b49-1d12-4e5b-8222-79c57e5b6b1c.png)



### 1.2. Proxy的set和get捕获器

◼ 如果我们想要侦听某些具体的操作，那么就可以在`handler`中添加对应的捕捉器（`Trap`）：

◼ `set`和`get`分别对应的是函数类型；

 `set`函数有四个参数：

- ✓ `target`：目标对象（侦听的对象）；
- ✓ `property`：将被设置的属性`key`；
- ✓ `value`：新属性值；
- ✓ `receiver`：调用的代理对象；

 `get`函数有三个参数：

- ✓ `target`：目标对象（侦听的对象）；
- ✓ `property`：被获取的属性`key`；
- ✓ `receiver`：调用的代理对象；

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682093116036-7a494fb5-35c8-4df9-9db1-39c83f63b0cf.png)



### 1.3. Proxy所有捕获器

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682093156588-aecb24c1-4aa4-4af4-8320-db1ac42926bf.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682093836616-565c9b47-b5b8-473d-997d-49192d6b4c28.png)



### 1.4. `Proxy`的`construct`和`apply`

◼ 当然，我们还会看到捕捉器中还有`construct`和`apply`，它们是应用于函数对象的：

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682093887559-e6e2996c-b560-4cfb-a695-c9c786321706.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682093973358-b9a3fd17-2b03-48ed-950b-b7d35330641a.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682093947709-4c360f1c-b272-4869-af65-5480a4fb6071.png)



## 2. Reflect

### 2.1. Reflect的作用

与 `Object.defineProperty()`的重要一个区别就是它返回的是一个`bool`值

◼ `Reflect`也是`ES6`新增的一个`API`，它是一个对象，字面的意思是反射。



◼ 那么这个`Reflect`有什么用呢？

- 它主要提供了很多操作`JavaScript`对象的方法，有点像`Object`中操作对象的方法；
- 比如`Reflect.getPrototypeOf(target)`类似于 `Object.getPrototypeOf()`；
- 比如`Reflect.defineProperty(target, propertyKey, attributes)`类似于`Object.defineProperty() ；`



◼ 如果我们有`Object`可以做这些操作，那么为什么还需要有`Reflect`这样的新增对象呢？

- 这是因为在早期的ECMA规范中没有考虑到这种对对象本身的操作如何设计会更加规范，所以将这些API放到了`Object`上面；
-  但是`Object`作为一个构造函数，这些操作实际上放到它身上并不合适；
-  另外还包含一些类似于 `in、delete`操作符，让`JS`看起来是会有一些奇怪的；
-  所以在`ES6`中新增了`Reflect`，让我们这些操作都集中到了`Reflect`对象上；
-  另外在使用`Proxy`时，可以做到不操作原对象；
-  返回 `bool` 值, 能够判断是否操作成功
-  `receiver`能够改变 可以决定对象访问器`setter`/`getter`的`this`指向



![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682094550201-b8dc4e0f-0f60-44b9-a6ef-c1248a3c39da.png)



### 2.2. Reflect的常见方法

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682093434329-45ddc351-e455-4ed4-a338-46b5cc0507a2.png)



### 2.3. Reflect的使用

那么我们可以将之前`Proxy`案例中对原对象的操作，都修改为`Reflect`来操作：

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682093511842-7e1fcccd-c26b-4cbf-a3d7-60ac168f4982.png)



### 2.4. Receiver的作用 (特殊业务需求)

◼ 我们发现在使用`getter`、`setter`的时候有一个`receiver`的参数，它的作用是什么呢？

- 如果我们的源对象（`obj`）有`setter`、`getter`的访问器属性，那么可以通过`receiver`来改变里面的`this`；

◼ 我们来看这样的一个对象

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682095866423-0592a7de-0c98-4049-a5b1-973a42d8f7e8.png)



在调用这一句代码`this._name = newValue` 时 , 会改变	`_name` 的值, 而这次改变不会被`proxy`监听到, 所以我们需要在 `Reflect` 中导入 `receiver`来改变对象访问器`setter`/`getter`的`this`指向, 这样便可以将其监听 . 



![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682096167510-c90b3a65-5e92-4a04-9ab4-3116166dd8fe.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682096223177-2a658246-0ba2-4f11-9da1-1ae48da7367f.png)



### 2.5. Reflect的construct (用的不多)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682096293518-c2725d4f-4e3b-4a6f-9f1e-b8ac32f1610c.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1682096323991-c399a3b1-1bc7-410b-b3c4-53356145a960.png)



上述代码中 `Person.call(this, name, age)`  

等于 `Reflect.construct(Person, [name, age], Student)`

> 创建一个`Student`类型的对象, 用的`Person`的代码