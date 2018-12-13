---
title: 《JavaScript 高级程序设计》 读书笔记--创建对象的演化
date: 2018-12-11 21:30:25
categories: 前端学习
tags:
  - JavaScript
  - 前端
  - 读书笔记
---

最近在复习《JavaScript 高级程序设计》看到对象一章，才发现自己在对象的创建和继承上的认识是多么的不足。于是写下这一篇作为自己的读书笔记，同时也加强自己的理解。

在自己真正有了一些项目经验后，再回头看，不由地觉得这本书写得真是好。还记得在前端小白的时代，看“高程”简直是不知所云，为什么要讲这么些基础语法呢？而且还十分枯燥。如今再看，原来是字字珠玑，把 JavaScript 这门语言的特性和细节全都交代得一清二楚。所以对于已经工作一两年的前端小伙伴，赶紧再去看看“高程”一定会有所收获的。

那么下面就进入正题吧。

---
说到在 JavaScript 中要创建一个对象，我们最先应该会想到下面两种方法。
1. 对象字面量： `let a = {}`，用一对花括号解决一切
2. 创建构造函数： `function A(){} / Class A {}`，用一个 `new` 解决一切

在“高程”中一共给我们整理出了 7 种创建对象的方法。别怕，虽然数量上有很多，但其实基本都为了解决使用过程中的问题的，我们顺着问题去理解就很容易了。

### 0. 花括号
- 产生的问题： 代码重复。

创建一个类，最简单的莫过于一对花括号。就像下面这样，简单、直接。
```JavaScript
let p1 = {
  name: 'KizunaAi',
  age: 2,
  sex: 'f',
  getName: function(){
    return this.name
  }
}
```

但是，当我们要创建 10 个类似于 `p1` 的对象时，代码的重复就成了一个头痛的事情了。于是，我们就利用函数和 `Object`，搭建出工厂模式。
<!-- more -->

### 1. 工厂模式
- 解决问题：重复代码问题。只要定义一次，就能创建相同格式的对象。
- 产生的问题：没有对象类型，实例都是 `Object` 类型；内部方法重复生成会占空间。

在**普通函数**内包装了一个 `Object`，然后通过 `new Object()` 返回我们要的实例。在软件工程中是个经典的模式，不过现在基本被构造函数模式代替了。

示例代码如下：
```JavaScript
function CreatePerson(name, age, sex){
  // 在函数内部生成 Object 的实例
  let o = new Object()
  o.name = name
  o.age = age
  o.sex = sex

  o.getName = function(){
    return this.name
  }

  return o
}

// 普通调用函数方法获取实例
let p1 = CreatePerson('KizunaAi', 2, 'f')
let p2 = CreatePerson('Luna', 100, 'f')
```

工厂模式彻底解决了代码重复的问题，但是留下实例无法确定对象类型的问题，这个主要是因为实例的 `constructor` 函数指向的是 `Object`，为了解决这个问题，就有了后面的构造函数方法。

### 2. 构造函数
- 解决问题：构造函数 `new` 出来的实例，有对象类型。即可以使用 `instanceof` 方法进行判断。
- 产生的问题：方法（函数）定义在构造函数内部时，每次创建实例时都会重复创建方法，因此仍然会有资源占用的问题。

```JavaScript
function Person(name, age, sex){
  this.name = name
  this.age = age
  this.sex = sex

  this.getName = function(){
    return this.name
  }
}

// 我们最常用的 new 来啦
let p1 = new Person('KizunaAi', 2, 'f')
let p2 = new Person('Luna', 100, 'f')
```

构造函数方法解决了实例的对象类型问题，也基本取代了工厂模式。但是对于每个实例的会创建各自独立的方法仍然没有解决。这个问题就交给了原型模式。

### 3. 原型模式
- 解决问题：实例的方法可以共享，可以避免资源占用的问题。
- 产生的问题：属性也会共享，那么一旦有一个实例修改了属性，所有的实例对应的属性也会被修改。

我们创建的每个函数都有一个 `prototype` 属性，这个属性的用处就是可以让所有的实例共享属性和方法。这样一来，我们把方法挂到 `prototype` 上就能避免实例方法的重复创建的问题了。

```JavaScript
function Person(){}

Person.prototype.name = 'KizunaAi'
Person.prototype.age = 2
Person.prototype.sex = 'f'
Person.prototype.getName = function(){
  return this.name
}

let p1 = new Person()
// p1.name = KizunaAi
```

原型模式解决了实例方法独自创建的问题，但是原型上的公共属性却也会被波及，但这个其实并不是什么问题。到这里基本问题就基本都解决了，只要优化一下即可。

### 4. 组合模式（构造 + 原型）
所以顺着上面的问题，就能明白为什么说属性要放在构造函数里，方法要挂到 `prototype` 上了。而这么一来，也就是我们用的最多的书写类的方法了。

```JavaScript
function Person(name, age, sex){
  this.name = name
  this.age = age
  this.sex = sex
}

Person.prototype.getName = function(){
  return this.name
}

let p1 = new Person('KizunaAi', 2, 'f')
let p2 = new Person('Luna', 100, 'f')
```

### 小结
尽管高程中创建对象有 7 中模式，但顺着问题就能发现**组合模式**解决了前面留下的所有问题。可以说是最合适的模式了。之后的模式其实都是前面模式的衍生和变种，感觉平时也用不上也就不再展开了。

说实在的，在日常的工作中会用一对花括号和一个 `new` 基本就能解决 90% 的问题了。真的要自己去了解为什么这么做的情况并不多（我自己除了在 Node 中会写写类，在前端感觉真的用不到，除非是自己写框架吧）

但有时候去了解一下常用的方法背后的故事，不也是一件挺有意思的事情吗？

---
其实我原本主要是去看继承的（这一块平时用得少，一直是一知半解的状态），在看的过程中发现继承与对象创建的关系十分密切。看完对象的创建再看继承会容易理解很多。所以下一篇就是继承的学习笔记了。