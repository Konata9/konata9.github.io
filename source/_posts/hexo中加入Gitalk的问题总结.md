---
title: hexo中加入Gitalk的问题总结
date: 2018-12-16 21:29:59
categories: 技术
tags:
  - hexo
  - Gitalk
---
最近因为换了新电脑，于是把 hexo 博客做了一个迁移。就借此机会顺便给自己的博客加入 Gitalk 作为评论的功能。但是在加入的过程中，遇到了几个问题，留作记录和备忘。

### 1. 在 hexo 中引入 Gitalk
我的博客使用的是 NexT 主题，在 NexT 主题下引入 Gitalk 具体可以参考这篇文章 [Hexo NexT主题中集成gitalk评论系统](https://asdfv1929.github.io/2018/01/20/gitalk/)。引入方式很简单，照着这篇文章做就行。

只是有一点需要注意的是，文章中提到的文件夹的位置是在 `themes/next/` 下的。

>新建/layout/_third-party/comments/gitalk.swig文件，并添加内容

比如像这步操作，就是要在 `themes/next` 文件夹下进行的。因为原文没有明说，所以一开始我是建在了根目录下（我一开始可能也没理解 T T）

### 2. Gitalk 引入后控制台 422 错误的解决方式
在上面引入 Gitalk 后，打开博客第一次会需要我们进行 GitHub 的认证，如果我们是按照官网（下面代码）或者网上其他的博客引入时，就会出现 `控制台 422` 的问题。

```JavaScript
var gitalk = new Gitalk({
  clientID: 'GitHub Application Client ID',
  clientSecret: 'GitHub Application Client Secret',
  repo: 'GitHub repo',
  owner: 'GitHub repo owner',
  admin: ['GitHub repo owner and collaborators, only these guys can initialize github issues'],
  //id: location.pathname 官方文档代码。由于 GitHub Issue Label 50 个字符的长度的限制，直接引用的话页面就会报 422 错误因此需要把这里 md5 化
  id: md5(location.pathname)     
  distractionFreeMode: false 
})

gitalk.render('gitalk-container')
```

问题的原因很简单，是由于 GitHub 对于 Issue Label 的 50 个字符限制，我们只需要让 `id` 小于 50 个字符即可。

网上给出的方法是使用 md5，但是 md5 的 js 库很不巧在 `unpkg` 上似乎没有，不能引用网上资源只能下载下来本地引入。但是网上的解决方法都没给出 `js` 文件本地引入的位置。害的我搞了很长时间。要在 NexT 中引入静态文件的话，文件我们可以放在 `themes/next/source/js/src` 下；而 html 中，是在 `themes/next/layout/_layout.swig` 中引入。只要注意引入位置和路径即可。

```html
  ...
  // 注意引入的位置和路径
  <script type="text/javascript" src="/js/src/md5.min.js"></script> 
  {% include '_third-party/comments/index.swig' %}
  ...
```

这么一来，我们重新编译就能解决 Gitalk 的问题了。这样一来，就可以愉快地让来看博客的人发表评论了～效果如下：
![](/images/gitalk-sample.png)