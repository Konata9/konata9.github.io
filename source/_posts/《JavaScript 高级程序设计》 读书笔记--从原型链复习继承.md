---
title: 《JavaScript 高级程序设计》 读书笔记--从原型链复习继承
date: 2018-12-17 22:46:30
categories: 前端学习
tags:
  - JavaScript
  - 前端
  - 读书笔记
---

这一篇进入正题来复习一下 JavaScript 中对象的继承。“高程”中一共列举了 6 种继承的方式。看起来是有些吓人，但仔细梳理就能发现其中也是有一个演变过程的。这篇笔记就是我自己对这个过程的理解。如果有不足的地方，还希望各位可以指出。

再次安利一下“高程”，真的写得非常棒。有一定基础和项目经验的同学绝对要去看一看，能提高不少。

## 基础概念
在进入继承之前，我们再把一些基本概念复习一下。

### 0. 构造函数、原型对象、实例
谈到对象，一定会出现这三个概念。在 JavaScript 中，**原型对象**不需要我们手动去定义，当我们定义一个类或者构造函数后，JavaScript 会自动生成对应的原型对象，我们可以通过 `prototype` 属性访问；我们写的 `function A(){}` 就是**构造函数**；而我们**用 `new` 调用构造函数返回的值就是对象的实例**。

```javaScript
// 构造函数：其实就是一般的函数。函数名大写只是一个约定规则而已。
// 事实上除了 new 之外，我们也可以像调用一般的函数一样使用。
// 在 ES6 中就是 Class 里面的 constructor
function A(){
  this.a = 'a'
}

// 原型对象：当我们定义对象的时候，就会生成一个对应的原型对象。
// 该函数的 prototype 属性就指向原型对象，这个就是原型链的精髓。
A.prototype

// 实例: 用 new 调用构造函数的返回值。
var a = new A()
```

### 1. 原型链
下面再来看一下原型链的概念。原型链的概念我们一定不会陌生。那么就不多说了，直接上图。这是一个最基本的原型链，我们要仔细理解这张图并且搞清楚 **实例**、**对象原型**、**构造函数**以及与 `Object` 之间的关系。
![](/images/prototype.png)

在复习完上面两个概念之后（特别是原型链），相信在后面理解继承的时候会有所帮助。如果在后面有所困惑的话，不妨回来看看基础概念。

下面就开始进入正题。

<!-- more -->

----

## JavaScript 中的继承
### 1. 原型链继承
我们对上面基础的例子做一个拓展。再加入一个对象 `B`，我们同样画成图。
![](/images/prototype2.png)

`A` 和 `B` 是互相独立的两个对象（类），并且它们都是 **继承** 了 JavaScript 的对象之祖——`Object`对象。请注意，在一般的对象中，就已经存在了一个 **对象** 与 **`Object`对象** 的继承关系了。

那么现在我们要让对象`B`去继承对象`A`，就可以模仿对象和`Object`的关系，修改`B`的原型对象的指向。
![](/images/prototype3.png)

这么一来，`B`就可以顺着原型链访问到`A`了。由于现在`B.prototype = A.prototype`了，那么在`B.prototype`上做的任何修改都会影响到`A.prototype`了。所以我们再稍微调整一下，让 `B.prototype = new A()` 。

这样，完整的原型链继承的关系图就出来了。
![](/images/prototype4.png)

简单的示例代码如下，小伙伴们可以在 Chrome 中试玩一下。
```javaScript
function A(){ this.a = 'a' }
function B(){ this.b = 'b' }

// 不要这么做，因为修改 B 的 prototype 会影响 A 的 prototype
// B.prototype = A.prototype
B.prototype = new A()
B.prototype.constructor = B
```

#### 对象的 constructor
在上面的示例代码中，最后一行我们对对象 B 的构造函数重新进行了赋值。这是因为，当我们改变了 `B.prototype` 的时候，会切断原来 B 的构造函数与 `B.prototype` 之间的联系。

虽然这个属性对我们的继承关系没有影响（`instanceof` 方法结果仍然正确）。但是从代码含义上来说，我们最好还是修改称为正确的指向。

另外，对于实例来说 `a.constructor.prototype === A.prototype // true`。即我们可以通过实例的构造函数去给对象原型添加属性和方法。尽管没人会推荐我们这么去做，但让属性指向正确的值会比较好。

和对象的创建一样，原型链的方法是比较简单的。但是也有一个明显的缺陷，就是“无法”对父类传不同的值。即 `B.prototype = new A(xx)` 时之后所有的 B 的示例都会带上这个值，因此就产生了局限性。

回想一下在创建对象时，我们是怎么解决这个问题的？

### 2. 构造函数继承
在创建对象时，我们知道不同的实例在创建时只要向构造函数中传入不同的值，就会得到不同的值。那么回到继承上，为了解决原型链继承无法向父类传递不同值的问题，我们同样也需要借助构造函数。

在进入正题前，我们再看一下构造函数，然后想一下如果**不用 new 调用构造函数会是怎样？**
下面是一个 `Person` 的构造函数。一般来说我们使用 `new` 关键字创建 `Person` 的实例。但是有没有想过，构造函数也是函数，如果我们不用 `new` 而是普通地调用会是怎么情况呢？
```javaScript
function Person(name, age, sex){
  this.name = name
  this.age = age
  this.sex = sex
}

// 直接调用会是怎么情况呢？
Person('Kizunaai', 2, 'female')
```

熟悉 `this` 特性的小伙伴肯定能反应过来。独立调用函数时，若在非严格模式下，`this` 指向的是 `window`（浏览器环境）。那么我们看一下 `window`。
![](/images/constructor.png)

`window` 中果然就有了 `age` 这个属性，并且值为 2。也就是说，直接调用构造函数就相当于把构造函数中的属性赋给调用它的对象了。

好，趁热打铁，我们直接来看代码。
```javaScript
function Person(name, age, sex){
  this.name = name
  this.age = age
  this.sex = sex
}

function VTuber(name, age, sex){
  // 调用 Person 的构造函数实际上就是把 Person 的值赋给 VTuber
  // 在 ES6 中就是 super()
  Person.call(this, name, age, sex)
}

var kizunaai = new VTuber('kizunaai', 2, 'female')
var luna = new VTuber('luna', 100, 'female')

kizunaai.name // 'kizunaai'
luna.name // 'luna'
```

我们在 Chrome 中分别打印一下之前的实例。可以看到 `VTuber` 的实例只是包含了 `Person` 的属性而已，而在原型链上两者是没有任何关系的。
![](/images/constructor2.png)

所以再想一下，构造函数的方法其实真的是“继承”吗？因为对于“子类”来说，是没有办法**调用父类原型**上的方法的。而在用构造函数创建对象时我们就已经知道，把方法写在构造函数里显然不是一个好的解决方法。

### 3. 组合式继承
既然原型链和构造函数正好能弥补互相之间的缺陷，组合起来我们能愉快地进行继承了。也没什么新的知识点，就直接上代码了。

```javaScript
function Person(name, age, sex){
  this.name = name
  this.age = age
  this.sex = sex
}

Person.prototype.sayHello = function(){
  return `${this.name} say hello ~`
}

function VTuber(name, age, sex){
  // 构造函数保证了不同值的传递
  Person.call(this, name, age, sex)
}

// 原型链保证了方法的传递（还有意义上）
VTuber.prototype = new Person()
VTuber.prototype.constructor = VTuber

var kizunaai = new VTuber('kizunaai', 2, 'female')
var luna = new VTuber('luna', 100, 'female')

kizunaai.name // 'kizunaai'
luna.sayHello() // 'luna say hello ~'
```

### 4. 寄生组合式继承
组合式继承是我们最常用的继承方法，几乎可以说是满足了我们的需求。硬要挑刺的话，也就是父类的构造函数调用两次的问题了。
```javaScript
// 以之前的代码为例
// 第一次在子类的构造函数中调用
Person.call(this, name, age, sex)

// 第二次在建立原型链时调用
VTuber.prototype = new Person()
```

其中第一次是一定省不掉的，要下功夫的话就是在第二次建立原型链的时候了。还是以前面的代码为例，我们就这么继承，数据结构会是怎么样的？
![](/images/combine.png)

可以看到在 `VTuber.prototype` 上也有 `name, age, sex` 三个属性，但实际上这三个属性根本没有意义。那么解决的思路就有了，我们需要借助一个空的对象来搭一座桥。（千万别说让 `VTuber.prototype = Person.prototype` 了，理由参考原型链那部分）

```javaScript
function Person(name, age, sex){
  this.name = name
  this.age = age
  this.sex = sex
}

Person.prototype.sayHello = function(){
  return `${this.name} say hello ~`
}

function VTuber(name, age, sex){
  // 构造函数保证了不同值的传递
  Person.call(this, name, age, sex)
}

// 我们要借用一个空对象作为过渡
// VTuber.prototype = new Person()
function A(){}
A.prototype = Person.prototype
VTuber.prototype = new A()

VTuber.prototype.constructor = VTuber

var kizunaai = new VTuber('kizunaai', 2, 'female')
var luna = new VTuber('luna', 100, 'female')

kizunaai.name // 'kizunaai'
luna.sayHello() // 'luna say hello ~'
```

上面我们借用了一个 `A` 来打了个桥。这样一来就在原型链上就没有多余的属性了。（其实两次构造函数是肯定要调的，只是第二次调谁的问题）
![](/images/combine2.png)

而这种搭桥的方式，在“高程”中也被称为是原型式继承。其中关于原型式和寄生式分别和原型链、构造函数相对应，感觉没有这两种直观而且也不常用（个人感觉）所以就不做展开了，有兴趣的小伙伴还是推荐去阅读“高程”。

### 小结
至此，有关于继承的笔记就到此为止。这一篇顺着对象的原型链的概念开始，介绍了 JavaScript 中对象继承的几种方式。其中组合继承的方式是我们最常见也是用的最广的，我们需要好好了解一下。

在看书的过程中，像这样给自己抛点问题，找一找方法间的演变脉络即很有趣也很容易理解。不知道各位小伙伴有什么好的学习方法呢？不妨互相交流一下吧～