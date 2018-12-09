---
title: 利用jsdoc为vue项目生成漂亮的文档
date: 2018-01-26 15:19:40
categories: 前端学习
tags:
  - JavaScript
  - 前端
  - Vue
---

jsdoc是前端自动生成文档所常用的工具。我们只要按照jsdoc的语法来书写注释就能顺利的生成一片很漂亮的文档。

只是jsdoc针对的是`js`文件，对于`.vue`文件似乎就有些无能为力了。不过既然`vue`这么人气，相信一定也会有相应的工具。在找了一圈之后，果然找到了一位前辈写的`jsdoc-vue`。

在研究了一下之后，发现这个工具主要解决了jsdoc不认`.vue`文件的问题。虽然可以让`.vue`文件自动生成文档，但生成出的文档样式却并不好看。于是就fork过来改造了一下。

废话不多说，先上一张生成的文档截图。
<!-- more -->
github地址：[jsdoc-vue 改造版](https://github.com/Konata9/jsdoc-vue)
![vue文档截图.png](http://upload-images.jianshu.io/upload_images/3101443-6df83ab7a3834c37.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 改造的部分主要有以下几点：
1. 使用了`jsdoc`字典，因此可以使用`jsdoc`自带的标签来进行编写。这样也便于生成需要的模板。
2. 生成文档采用了[minami](https://github.com/Nijikokun/minami)模板
3. 升级了 vue-template-complier 和 jsdoc 模块

##### 安装使用方法
```
git clone git@github.com:Konata9/jsdoc-vue.git

npm install 
```

这个改造后的版本我自己用着还算比较满意，比较重要的如`props`和`components`组件都可以利用jsdoc自带的语法比较清楚的表示出来。

如果各位喜欢的话，希望可以点一个star。当然，如果有改进的地方也希望各位可以指点一下。