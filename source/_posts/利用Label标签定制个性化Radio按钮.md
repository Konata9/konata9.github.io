---
title: 利用Label标签定制个性化Radio按钮
date: 2019-08-28 21:56:30
tcategories: 前端学习
tags:
  - JavaScript
  - 前端
  - React
---

<div style="text-align:center">
  <img width="100%" src="https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1567010872446&di=643c73b12b35ff32d8fec3e5db6d15fe&imgtype=0&src=http%3A%2F%2F04imgmini.eastday.com%2Fmobile%2F20190507%2F20190507194423_ce095d193a1e71b6856248d8d7bfbbf6_1.jpeg">
</div>

在最近的项目中，遇到了类似下图的单选按钮。
![](/images/pay-radio.jpg)

Radio 按钮的默认样式并不好看，经常成为被修改样式的对象。而常用的 antd 中，Radio 组件也没有提供可用于自定义的属性。当然，这些是难不倒各位前端小伙伴的，只要利用好 `label` 标签就能很简单地实现一个个性化 Radio 按钮组件。

CSS3 目前基本都得到了支持，所以配合 CSS3 就能减少我们在 JS 上的处理。这一次主要涉及如下知识点：

- `label` 标签的使用
- `:checked` 伪类的使用
- `::before/after` 伪元素的使用

实际完成的效果如下图（由于偷懒，很多就用文字代替了）：
![](/images/radio-example.jpg)

<!-- more -->

想看代码的小伙伴也可以来这里：[利用 Label 定制个性化 Radio 按钮](https://codepen.io/Konata9/pen/QWLvEyb?editors=0111)

整个 Radio 的核心思想，就是利用 `label` 与 radio 的互相关联。这样在点击 `label` 的时候，radio 按钮也会跟着联动。`label` 与表单控件的关联分为显示和隐式两种。显示即我们熟悉的 `for` 属性关联表单控件的 `id`，而隐式则是把表单控件包裹在 `label` 标签中。在我们的例子中，采用的是隐式关联的方法。

```javascript
<label class="label-wrapper" style={labelStyle} onChange={handleChange}>
  <input type="radio" id={id} name={name} value={value} />
  // 辅助的 div 在例子里面用来显示边框等自定义样式
  <div>{content}</div>
</label>
```

为了方便对样式的控制，我们需要调整一下 `label` 的默认样式。

```css
.label-wrapper {
  display: block;
  position: relative;
}
```

下一步就是利用到 `:checked` 伪类，来控制选中和非选中的样式。对应我们的例子，就是 `input` 下面的 `div` 的样式。而对于简单的图案，如边框、右上角的选中标示，其实还可以利用 `::before/after` 伪元素来实现。

```css
input:checked + div {
  // 通过 padding 解决抖动问题
  padding: 0;
  position: relative;
  border-radius: 10px;
  border: 5px solid lightblue;

  &::after {
    content: "@";
    color: #fff;
    position: absolute;
    top: -10px;
    right: 1px;
  }

  &::before {
    content: "";
    width: 0px;
    height: 0px;
    border: 15px solid lightblue;
    border-left-color: transparent;
    border-bottom-color: transparent;
    position: absolute;
    top: 0;
    right: 0;
  }
}
```

如此一来，一个简单的自定义 Radio 按钮就实现出来了。根据需求我们可以监听 `onChange` 事件等来实现我们想要的功能。另外，只要利用 radio 相同 `name` 就会互斥的性质，就能实现按钮组的效果。

最后再提一下，想看代码的小伙伴也可以来这里：[利用 Label 定制个性化 Radio 按钮](https://codepen.io/Konata9/pen/QWLvEyb?editors=0111)

##### 总结：

这一次利用 CSS 伪类来实现自定义 Radio 按钮，主要是受到最近刷题中 CSS 题目的启发。虽然现在前端的重点在 JavaScript 上，但 HTML 与 CSS 仍然是逃不开的内容。CSS3 提供了许多伪类和新的属性，利用好伪类可以减少我们在 JavaScript 上的工作量。所以在空闲之时不妨多翻两眼 CSS3 吧。
