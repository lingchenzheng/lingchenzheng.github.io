---
title: node-gyp安装
category: 其他
tag: 工具
---

##### 介绍
node-gyp，是由于node程序中需要调用一些其他语言编写的 工具 甚至是dll，需要先编译一下，否则就会有跨平台的问题，例如在windows上运行的软件copy到mac上就不能用了，但是如果源码支持，编译一下，在mac上还是可以用的。node-gyp在较新的Node版本中都是自带的（平台相关），用来编译原生C++模块。

##### node-gyp的依赖

以windows为例

通过命令行一键安装

```shell
npm install --global --production windows-build-tools
```
`windows-build-tools`会安装如下包

-   python2.7(不支持3.x)
-   visual C++ Build Tools(vistual studio2015及以上)
-   .net framework 4.5.1

注意：需要以管理的方式运行上面的命令。

##### 安装node-gyp

```shell
npm i -g node-gyp
```

注意：安装`node-gyp`之前，确认`node`是32位的，如果你的电脑安装的是64位的，可以通过nvm来安装管理node版本。


##### 验证是否安装完成

```shell
node-gyp list
```
