---
title: 使用Vue+PouchDB做一个本地的ToDoList
date: 2018-02-22 22:56:41
categories: 前端学习
tags:
  - JavaScript
  - 前端
  - Vue
---
首先在这里给大家拜个晚年，祝大家新年愉快，编程功力节节高升~~

我自己有制定年度规划的习惯，之前也一直在用OneNote在做。OneNote虽然好用，可是没有相关统计功能。这样就不容易把握计划制定的是否合理。于是就趁着放假，顺手做一个简单的ToDoList小程序。

##### 需求分析
这个项目就先叫他EasyTodoList吧。不过先别急着动手，先利用脑图简单整理一下需求。这样既可以清楚地知道需要哪些东西以及从哪下手，也可以防止在做的过程中歪楼。
![EasyTodoList 项目需求](http://upload-images.jianshu.io/upload_images/3101443-ab9639355702fea7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
<!-- more -->

##### 技术选择
技术方面`Vue`和`vue-router`相信不会有什么问题，毕竟是现在的主流之一。所以这一次的主要问题是在存储的选择上。

首先，先去掉MySQL和MongoDB，因为这仅仅只是一个小项目，数据量也不是很大。因此没有必要上后端的东西。
那么剩下就是客户端存储了。在客户端存储中，storage只能进行简单的`key-value`存储，没有查询和统计的功能；而WebSQL虽然用起来很爽，但已经是不在维护的项目了，所以也不推荐使用了；最后就是IndexedDB了，IndexedDB是目前比较推荐的客户端存储技术了。类似于MondgoDB的NoSQL数据库。因为原生API相对繁琐，为了简化开发，这里就使用二次开发的[PocuhDB](https://pouchdb.com/)来作为客户端存储（API调用很简单，参考一下官网就能搞定）。（关于客户端存储的相关内容可以查看我之前的文章[客户端存储技术](https://www.jianshu.com/p/a363b60cefcd)）

需要注意的是，IndexedDB也是客户端存储的一种，自然也有客户端存储的特性。那么当改变浏览器或者改变监听端口时，之前存储的数据自然也就消失不见了（相当于重新换了个数据库），这在使用上或多或少带来了不便。

##### 效果展示
先来看一下最终做出来的效果。
![效果图](http://upload-images.jianshu.io/upload_images/3101443-4ee06c0f00c99327.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

整个项目比较简单，只有总览以及任务列表两个页面。在添加了任务分类以及分类下的任务后，在总览页面就会有相关统计。在设置任务完成后，也会统计完成与未完成的任务的比例以及图表。图表部分是用Echarts来进行绘制的，绘制的方法也可以参考[官方文档](http://echarts.baidu.com/)。

因为代码量不多也就不在这里多啰嗦了。详细代码可以来我的GitHub上参观指导。
项目地址：[EasyTodoList](https://github.com/Konata9/EasyTodoList)

##### 总结
整个项目在技术上没有难点，是个很容易撸的项目。结合了前端存储（IndexedDB），虽然简化了开发也带来了使用上的不便。不过作为一个练手的项目应该是比较不错的了。因为存储的问题非常尴尬，所以接下来考虑移植到Electron上，直接做成一个App来避免这个问题。