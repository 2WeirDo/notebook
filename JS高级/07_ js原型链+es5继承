## 1. 原型和原型链



### 1.1. 概念理解

原型（`prototype`）和原型链（`prototype chain`）是`JavaScript`中的核心概念，它们与对象的继承机制密切相关。



原型（`prototype`）：在`JavaScript`中，每个对象都有一个关联的原型。原型本身也是一个对象，包含了一组属性和方法。当我们试图访问一个对象的属性或方法时，如果该对象本身没有这个属性或方法，`JavaScript`引擎会去对象的原型上查找。原型充当了一种共享资源的角色，使得对象可以继承其原型的属性和方法。



原型链（`prototype chain`）：原型链是由一系列原型对象组成的链状结构。当我们访问一个对象的属性或方法时，`JavaScript`引擎会沿着原型链向上查找，直到找到相应的属性或方法，或者达到原型链的顶端（`null`）。原型链是`JavaScript`实现继承和属性查找的基础。



原型链的工作原理如下：



1. 当试图访问一个对象的属性或方法时，首先在对象自身查找；
2. 如果对象自身没有找到，那么沿着原型链向上查找，先查找原型对象；
3. 如果原型对象也没有找到，继续沿着原型链查找，直至达到原型链的顶端（`null`）；
4. 如果在整个原型链上都没有找到相应的属性或方法，则返回`undefined`。



这种基于原型和原型链的继承机制使得`JavaScript`的对象系统具有很大的灵活性和可扩展性。



![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1681613120263-15924d1e-a714-4746-b142-332ed5f85ddf.png)





### 1.2. 对象的原型 (`__proto__`)

◼ `JavaScript`当中每个对象都有一个特殊的内置属性 [[`prototype`]]，这个特殊的对象可以指向另外一个对象。

◼ 那么这个对象有什么用呢？

- 当我们通过引用对象的属性`key`来获取一个value时，它会触发 [[`Get`]]的操作；
- 这个操作会首先检查该对象是否有对应的属性，如果有的话就使用它；
- 如果对象中没有改属性，那么会访问对象[[`prototype`]]内置属性指向的对象上的属性；

◼ 那么如果通过字面量直接创建一个对象，这个对象也会有这样的属性吗？如果有，应该如何获取这个属性呢？

* 答案是有的，只要是对象都会有这样的一个内置属性；

◼ 获取的方式有两种：

- 方式一：通过对象的` __proto__` 属性可以获取到（但是这个是早期浏览器自己添加的，存在一定的兼容性问题）；
- 方式二：通过 `Object.getPrototypeOf() `方法可以获取到；



### 1.3. 函数的原型 (`prototype`)

所有的函数都有一个`prototype`的属性（注意：不是`__proto__`）

>  对象没有这个属性



![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1681465353992-68de86a2-a6a6-49d5-9301-d3324348f67c.png)



### 1.4. 再看`new`操作符

1.在内存中创建一个新的对象（空对象）；

2.这个对象内部的[[`prototype`]]属性(也就是`__proto__`)会被赋值为该构造函数的`prototype`属性





### 1.5. constructor属性

◼ 事实上原型对象(`prototype`)上面是有一个属性的：`constructor`

- 默认情况下原型上都会添加一个属性叫做`constructor`，这个`constructor`指向当前的函数对象；
- 如果我们需要在原型上添加过多的属性，通常我们会重写整个原型对象：
- 默认情况下, 原生的`constructor`属性是不可枚举的.
- 如果希望解决这个问题, 就可以使用我们前面介绍的`Object.defineProperty()`函数了.



![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1681465693105-8a4a425f-3c2b-4904-ace0-84bc07da73f4.png)



有没有办法让所有的对象去共享某些函数呢?

- 可以，将这些函数放到`Person.prototype`的对象上即可；



![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1681466085112-1ff455c0-a94a-4ca0-b9ae-d2026babecaf.png)





### 1.6. `Object`的原型 --->`null`

◼ 那么什么地方是原型链的尽头呢？那个对象是否也是有原型`__proto__`属性呢？

◼ 我们会发现它打印的是 `[Object: null prototype] {}`

- 事实上这个原型就是我们最顶层的原型了
- 从`Object`直接创建出来的对象的原型都是 `[Object: null prototype] {}`。

◼ 那么我们可能会问题： `[Object: null prototype] {}` 原型有什么特殊吗？

- 特殊一：该对象有原型属性，但是它的原型属性(`__proto__`)已经指向的是`null`，也就是已经是顶层原型了；
- 特殊二：该对象上有很多默认的属性和方法



`Object`**是所有类的父类**

**原型链最顶层的原型对象就是**`Object`**的原型对象**







## 2. es5 继承的各个方案及其优缺点 🔥🔥🔥

在`JavaScript`中，有多种实现继承的方法，以下是一些常见的继承方案及其优缺点：



### 2.1. 借用构造函数继承

通过在子类构造函数内调用父类构造函数，并使用`call`或`apply`方法绑定子类实例的上下文。

- 优点：解决了原型链继承中子类实例共享引用属性的问题；可以向父类构造函数传递参数。
- 缺点：父类的方法不能复用，每个子类实例都会拥有自己的一份方法副本。

```javascript
  function Parent1(){
    this.name = 'parent1';
  }
  function Child1(){
    Parent1.call(this);
    this.type = 'child1'
  }
  console.log(new Child1);
```





### 2.2. 原型链继承

通过将子类的原型对象设置为父类的实例来实现继承。

- 优点：实现简单；父类和子类之间的方法复用。
- 缺点：子类实例共享父类实例的引用属性，导致一个实例修改属性值可能影响其他实例；子类无法向父类构造函数传递参数。

```javascript
  function Parent2() {
    this.name = 'parent2';
    this.play = [1, 2, 3]
  }
  function Child2() {
    this.type = 'child2';
  }
  Child2.prototype = new Parent2();

  console.log(new Child2());
```







### 2.3. 组合继承

结合原型链继承和借用构造函数继承的方法，通过原型链实现方法的继承，通过借用构造函数实现属性的继承。

- 优点：解决了原型链继承和借用构造函数继承的问题；实现了方法复用；可以向父类构造函数传递参数。
- 缺点：父类构造函数被调用两次，一次在子类构造函数内，一次在设置子类原型对象时。

```javascript
  function Parent3 () {
    this.name = 'parent3';
    this.play = [1, 2, 3];
  }
  function Child3() {
    Parent3.call(this);
    this.type = 'child3';
  }
  Child3.prototype = new Parent3();
```

之前的问题都得以解决。但是这里又徒增了一个新问题，那就是Parent3的构造函数会多执行了一次

（`Child3.prototype = new Parent3()`;）。这是我们不愿看到的。那么如何解决这个





### 2.4. 寄生组合式继承 🔥

结合组合继承和寄生式继承的优点，通过原型链实现方法的继承，通过借用构造函数实现属性的继承，同时避免了父类构造函数被调用两次的问题。

- 优点：解决了原型链继承和借用构造函数继承的问题；实现了方法复用；可以向父类构造函数传递参数；避免了父类构造函数被调用两次。
- 缺点：实现相对复杂。



下面是寄生组合式继承的示例代码：

```javascript
function Parent(name) {
  this.name = name;
}

Parent.prototype.sayName = function() {
  console.log(this.name);
};

function Child(name, age) {
  Parent.call(this, name); // 借用构造函数继承父类属性
  this.age = age;
}

// 创建一个新对象，其原型指向Parent.prototype，避免了构造函数被调用两次
Child.prototype = Object.create(Parent.prototype);

// 使用Object.defineProperty设置constructor属性，保持其不可遍历性
Object.defineProperty(Child.prototype, 'constructor', {
  value: Child,
  enumerable: false,
  writable: true,
  configurable: true
});

Child.prototype.sayAge = function() {
  console.log(this.age);
};
```

 

这个示例中，我们使用Object.create(Parent.prototype)创建一个新对象，并将其设置为子类的原型。这样可以避免父类构造函数在设置子类原型时被调用。同时，我们使用Object.defineProperty方法设置constructor属性，使其不可遍历。这种寄生组合式继承方法是目前被认为最理想的JavaScript继承实现方式，因为它既实现了方法复用，又可以传递参数，同时避免了父类构造函数被调用两次的问题。



## 3. 对象的方法补充 (可以查阅MDN)

### 3.1. `hasOwnProperty`

- 对象是否有某一个属于自己的属性（不是在原型上的属性）



### 3.2. `in` / `for in` 操作符

-  判断某个属性是否在某个对象或者对象的原型上



### 3.3. `instanceof`(手动实现)

-  用于检测构造函数（`Person`、`Student`类）的`pototype`，是否出现在某个实例对象的原型链上
- 由于任意对象（除了`null`）都是`Object`的实例   ,    所以`instanceof`运算符可以判断一个值是否为非`null`的对象。
- 注意，`instanceof`运算符只能用于对象，不适用原始类型的值。
- 对于`undefined`和`null`，`instanceof`运算符总是返回`false`。

手动实现`instanceof`

```javascript
function myInstanceof(left, right) {
    //基本数据类型直接返回false
    if(typeof left !== 'object' || left === null) return false;
    //getProtypeOf是Object对象自带的一个方法，能够拿到参数的原型对象
    let proto = Object.getPrototypeOf(left);
    while(true) {
        //查找到尽头，还没找到
        if(proto == null) return false;
        //找到相同的原型对象
        if(proto == right.prototype) return true;
        proto = Object.getPrototypeOf(proto);
    }
}
```

测试

```javascript
console.log(myInstanceof("111", String)); //false
console.log(myInstanceof(new String("111"), String));//true
```



### 3.4. `isPrototypeOf()`

- 某实例对象是否为参数对象的原型
- 用于检测某个对象，是否出现在某个实例对象的原型链上



### 3.5. `Object.getOwnPropertyNames()`

- 该返回一个数组，成员是参数对象本身的所有属性的键名(包含不可枚举的属性)，不包含继承的属性键名。
- 只获取那些可以遍历的属性，使用`Object.keys`方法。



### 3.6.  `in` 运算符和 `for...in `循环

- `in`运算符返回一个布尔值，表示一个对象是否具有某个属性。它不区分该属性是对象自身的属性，还是继承的属性。`in`运算符常用于检查一个属性是否存在。
- 获得对象的所有可遍历属性（不管是自身的还是继承的），可以使用`for...in`循环。
- `for in`不仅可以循环枚举自身属性还可以枚举原型链中的属性
- 为了在`for...in`循环中获得对象自身的属性，可以采用`hasOwnProperty`方法判断一下。



```javascript
// 1.hasOwnProperty
console.log(info.hasOwnProperty("name")) // false
console.log(info.hasOwnProperty("address")) // true

// 2.in操作符
console.log("name" in info)
console.log("address" in info)
// 注意: for in遍历不仅仅是自己对象上的内容, 也包括原型对象上的内容
for (var key in info) {
  console.log(key)
}

// 3.instanceof
// instanceof用于判断对象和类(构造函数)之间的关系
function Person() {}
function Student() {}
inherit(Student, Person)

// stu实例(instance)对象
var stu = new Student()
console.log(stu instanceof Student)
console.log(stu instanceof Person)
console.log(stu instanceof Object)
console.log(stu instanceof Array)

// 4.isPrototypeOf 
// isPrototypeOf() 方法用于测试一个对象是否存在于另一个对象的原型链上。
console.log(Student.prototype.isPrototypeOf(stu))  // true
console.log(Person.prototype.isPrototypeOf(stu))  // true

// 可以用于判断对象之间的继承
console.log(obj.isPrototypeOf(info))
```



- 获得对象的所有可枚举属性(不管是自身的还是继承的)   ,   可以使用以上函数.

```javascript
// 主要应用 for...in 循环
function getAllKeysIncludingInherited(obj) {
  const keys = [];
  for (const key in obj) {
    if (!obj.hasOwnProperty(key)) {
      keys.push(key);
    }
  }
  return keys;
}
```



- 获得对象的所有属性（不管是自身的还是继承的，也不管是否可枚举(遍历)），可以使用下面的函数。

```javascript
function inheritedPropertyNames(obj) {
  var props = {};
  while(obj) {
    Object.getOwnPropertyNames(obj).forEach(function(p) {
      props[p] = true;
    });
    obj = Object.getPrototypeOf(obj);
  }
  return Object.getOwnPropertyNames(props);
}
```





## 4. 原型继承关系 🔥



![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1681467645405-89504de6-10c3-451e-9edb-6b5bca99ebeb.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/29006943/1681467680596-082ff270-d773-4aff-a1e3-9f719a554d1a.png) 