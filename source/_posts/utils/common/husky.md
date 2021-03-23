---
title: husky 基本使用
category: 通用工具
tag: husky
---

## 介绍

> husky 是一个前端工具，用来给启用 git 的钩子函数，比如我们在代码提交之前，需要对代码做检查，格式化，或者对提交信息校验等等。

## 使用（本教程针对 husky5）

### 安装

#### 1、使用 npm 安装

##### 自动安装

```bash
# 安装并初始化，运行如下命令，将会安装husky5，并在项目根目录创建`./husky`文件夹，并添加`pre-commit`钩子
npm install husky --save-dev && npx husky init

# 添加钩子，如下
npx husky add .husky/commit-msg 'npx --no-install commitlint --edit $1'
```

##### 手动安装

-   1、安装 husky

```bash
npm i husky -D
```

-   2、启用 git hooks

```bash
npx husky install
```

-   3、为了别人拉去项目时自动化启用钩子，函数，修改 paackage.json

```json
{
    "scripts": {
        "prepare": "husky install"
    }
}
```

#### 2、使用 yarn 安装

-   1、安装 husky

```bash
yarn add husky --dev
```

-   2、启用 git hooks

```bash
yarn husky install
```

-   3、为了别人拉去项目时自动化启用钩子，函数，修改 paackage.json

```json
//yarn 不支持prepare钩子函数，所以使用postinstall代替
{
    "scripts": {
        "postinstall": "husky install"
    }
}
```

### 其他

#### 1、跳过

```shell
# 可以通过--no-verify参数跳过pre-commit和commit-msg钩子的执行
git commit -m "测试" --no-verify
```

#### 2、当使用 yarn 时，在 Windows 上运行 hook 可能失败，推荐如下做法

-   1、创建`.husky/common.sh`文件

```shell
command_exists () {
  command -v "$1" >/dev/null 2>&1
}

# Workaround for Windows 10, Git Bash and Yarn
if command_exists winpty && test -t 1; then
  exec < /dev/tty
fi
```

-   2、在钩子里面加载运行 commands

```shell
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"
. "$(dirname "$0")/common.sh"
yarn run test

```

> 一般与`lint-staged`搭配使用，只对暂存区的文件进行校验或者格式化
