---
title: 在 npm 上发布自己的包——typecheck
date: 2019-01-24 17:56:42
categories: 前端学习
tags:
  - JavaScript
  - 前端
  - npm
---

如今做前端不可能不知道 npm，我们每天光是 `npm install` 这个命令可能就要敲不下十次。在 npm 上发布一个自己的包可能也是我们做前端的目标之一吧。最近在项目中封装了两个比较通用的库，所以就先挑一个简单的拿来尝试一下。

## 关于 `package.json`
npm 项目的介绍文件主要就是 `package.json`。这个文件一般在我们创建项目时就会生成（除非你不使用 `npm init`）。文件的内容相信大家也不陌生。如果是做发布到 npm 上就要看一下前面的内容。

```json
{
  "name": "@konata9/typecheck.js", // 项目名称，建议带上前缀现在 npm 上名字类似的包实在太多了。名称重复或太过相似就会被打回
  "version": "1.1.0", // 每次发布的时候都必须更新版本号
  "description": "JavaScript typecheck",
  "main": "./dist/index.js", // 项目的入口，我们每次 import 或 require 时找的那个入口文件
  "files": [
    "dist" // 真正传到 npm 上的文件或文件夹。如果不设置，就是当前整个项目文件夹
  ],
  "scripts": {
    "build": "webpack --config webpack.config.js",
    "test": "./node_modules/mocha/bin/mocha --compilers js:@babel/register test/typecheck.test.js"
  },
  "repository": {
    // GitHub 项目地址，写上的话会在页面的右边显示。如果是先在 GitHub 上建好项目后再 npm init 的话，这里会自动使用 GitHub 上 repo 的信息
    "type": "git",
    "url": "git+https://github.com/Konata9/typecheck.git"
  },
  "keywords": [
    // 项目的关键词，有利于 npm 的搜索
    "TypeCheck",
    "typecheck",
    "JavaScript",
    "type"
  ],
  "author": "Konata9",
  "license": "MIT",
  "bugs": {
    "url": "https://github.com/Konata9/typecheck/issues"
  },
  "homepage": "https://github.com/Konata9/typecheck#readme",
  "devDependencies": {
    "@babel/core": "^7.2.2",
    "@babel/preset-env": "^7.2.3",
    "@babel/register": "^7.0.0",
    "babel-loader": "^8.0.5",
    "chai": "^4.2.0",
    "mocha": "^5.2.0",
    "webpack": "^4.28.4",
    "webpack-cli": "^3.2.1"
  },
  "dependencies": {}
}
```

## 项目结构
下面就来看一下整个项目的结构。因为这是一个试水项目，因此结构也不是很复杂。不过麻雀虽小，五脏俱全。该有的应该也都有了。整个项目用到了 `webpack` 进行打包（使用了 ES6 语法）、`mocha` 进行了单元测试（单元测试真是好东西啊）。
<!-- more -->
项目的目录结构如下，项目源码非常简单在 GitHub 上 [typecheck](https://github.com/Konata9/typecheck)
```
typecheck
|-- dist // 最终打包结果，也是上传到 npm 的文件
|-- node_modules
|-- src // 项目源码
  |-- typecheck.js // 库文件
|-- test // 测试文件
  |-- typecheck.test.js // 测试文件
|-- .babelrc
|-- .gitignore
|-- index.js // webpack 的入口文件
|-- package.json // 项目的描述文件 npm init 生成
|-- README.md
|-- webpack.config.js
```

## publish 时的注意事项
在把包发布到 npm 之前，有几件事情需要我们注意一下。首先我们要把源切回到 npm，不然是无法发布的。
```shell
# 先检查一下源是否正确
npm config get registry

# 如果源不对，就要切回 npm 源
npm set registry https://registry.npmjs.org/
```

在这步完成后，我们就要去 [npm](https://www.npmjs.com/) 官网上注册一个账号。注册完成后就可以在命令行里进行登陆（有账户的可以直接跳过）。
```shell
# 查看当前账户
npm whoami

# 添加用户，输入刚才之前在 npm 官网上注册的账户和密码
# 最后还要输一个邮箱，会显示在项目的页面上
npm adduser

# 发布包，第一次要加上 --access=publish (除非你出钱买了私有账户)
npm publish --access=public
```

我自己在 `publish` 时，遇上过 403 或者 402 的问题。403 主要的原因是包名重复或者太接近，一般我们加上自己的用户名作为域就行；402 一般就是没有加上 `--access=public`。

发布完成后就可以去项目的发布页查看到发布的包了。[typecheck](https://www.npmjs.com/package/@konata9/typecheck.js)

![](http://plx497mwo.bkt.clouddn.com/npm-sample.png)

## `README.md` 的书写

README 的好坏决定别人对于项目采用程度。不过这方面我自己也没什么经验，就不多说了。网上倒是有不少关于文章可以参考，比如这篇文章 [如何写好Github中的readme？](https://www.zhihu.com/question/29100816)。当然也可以参考一下 GitHub 上优秀项目的 Readme。