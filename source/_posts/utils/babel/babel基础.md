---
title: babel基础
category: babel
tag: 工具
date: 2019/12/1
---

##### 简介

我们知道 ECMA 组织每年都会发布 es 的新版本，新版本中会给语言添加新的特性，而这些新的特性浏览器并不一定支持，尤其是一些特性还在草案阶段。但是如果我们想使用这些特性该怎么办？于是 babel 就是出现了。

babel 是一个 javascript 编译器，主要用于将 ECMAScript 2015+ 版本的代码转换为向后兼容的 JavaScript 语法。

babel 是一个工具链，基于插件的，也就是说，它本身并不会进行转换，也就是说如果不对 babel 做任何配置，一段代码经过 babel 处理不会发生任何变化。

![](/images/util/babel/babel基础/1.png)

##### 使用场景

-   将 ES6+语法转换成 ES5 的语法，比如箭头函数，拓展运算符等等。
-   对一些新加的 api 进行 polyfill，如 Object.assgin 方法。
-   react 框架中 jsx 语法转换
-   typescript 转 javascript

#### 基本使用

babel 是基于 nodejs 的，要使用 babel 首先得安装 node。

1. 命令行使用

```bash
npm install --save-dev @babel/core @babel/cli
./node_modules/.bin/babel src --out-dir lib
```

如果是 npm5.2 及以上，可以使用 npx

```bash
npm install --save-dev @babel/core @babel/cli
npx babel src --out-dir lib
```

下面是我们对一段代码通过 babel 来转换
![](/images/util/babel/babel基础/2.png)

上图可以看到，我们没有配置插件时，代码并没有改变(除了多加了个分号)

```bash
npm install --save-dev @babel/plugin-transform-arrow-functions
npx babel src --out-dir lib --plugins=@babel/plugin-transform-arrow-functions
```

![](/images/util/babel/babel基础/2.png)

如上图，加上插件后，将箭头函数转换成 ES5 的函数了

2. babel 配置文件

上面，我们将插件放在命令行后面，很麻烦。于是 babel 提供了以配置文件的方式让我们来配置 babel 的参数。

babel 提供了几种配置方式：
