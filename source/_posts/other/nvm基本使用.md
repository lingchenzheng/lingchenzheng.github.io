---
title: nvm基本使用
category: 其他
tag: 工具
---

##### 介绍

nvm 是 node 版本管理工作，通过 nvm 我们可以在同一台电脑上安装多个版本的 node 并且可以在不同版本中切换。

##### 安装

可以下载免安装版(绿色版)和安装版本(建议)

以 windows 为例[下载地址](https://github.com/coreybutler/nvm-windows/releases)

选择`nvm-setup.zip`

1. 双击 exe 文件

2. 选择接受

![安装nvm](/images/other/nvm基本使用/1.png)

3. 选择 nvm 安装路径

![安装nvm](/images/other/nvm基本使用/2.png)

4. 选择 node 安装路径

![安装nvm](/images/other/nvm基本使用/3.png)

5. 点击`Next`完成安装

##### 使用

1. 查看可选 node 版本列表

语法：`nvm list [options]`

2. 安装

语法：`nvm install [version] [arch]`

`version`为版本号，`arch`为位，如果不指定版本，则默认安装最新稳定版本，不指定 arch 则默认安装系统位数版本

例子

```shell
#安装12.18.3版本
nvm install 12.18.3
#安装32位12.18.3版本
nvm install 12.18.3 32
```

3. 使用

语法：`nvm use [version] [arch]`

例子

```shell
#使用12.18.3版本
nvm use 12.18.3
#使用32位12.18.3版本
nvm use 12.18.3 32
```

4. 卸载

语法：`nvm uninstall [version] [arch]`

例子

```shell
#卸载12.18.3版本
nvm uninstall 12.18.3
#卸载32位12.18.3版本
nvm uninstall 12.18.3 32
```

5. 其他命令

`nvm arch`:显示 node 运行 32 还是 64 位

`nvm on`:开启 node 版本管理

`nvm off`:关闭 node 版本管理
