---
title: lerna基础
category: lerna
tag: 工具
---

## 概念

lerna 是一个用来优化管理 multi-package 项目（一般指 monorepo 项目）的工具，它内部是调用 git 与 npm，相当于对它们的命令行进行了高级封装。

---

## 使用

### 可以全局安装

```shell
npm i -g lerna
// or
yarn add -g lerna

//使用
mkdir <project-name> && cd <project-name>
lerna init
```

### 直接使用 npx

```shell
npx lerna init
```

---

## 两种模式 fixed/independent

### fixed 模式(默认是这个模式)

> fixed 模式所有 package 使用同一个版本，这个版本号维护在项目根目录的`lerna.json`文件里，这意味着，当你有一个模块更新后，你运行`lerna publish`发布时，所有模块的版本都被更新。

### independent 模式

```shell
lerna init --independent
```

> 独立模式允许每个模块拥有独立的版本号，版本号维护在每个模块的`package.json`文件中

## 配置文件`lerna.json`

```json
{
    "version": "1.1.3",
    "npmClient": "npm",
    "command": {
        "publish": {
            "ignoreChanges": ["ignored-file", "*.md"],
            "message": "chore(release): publish",
            "registry": "https://npm.pkg.github.com"
        },
        "bootstrap": {
            "ignore": "component-*",
            "npmClientArgs": ["--no-package-lock"]
        }
    },
    "packages": ["packages/*"]
}
```

-   `version`:当前仓库的版本
-   `npmClient`:指定命令行客户端，默认是`npm`，可以指定为`yarn`
-   `command.publish.ignoreChanges`:一个 globs 匹配规则的数组，将不会被包含在`lerna changed/publish`，比如你只是修改了 readme 文件，这时并不需要发布一个新版本。
-   `command.publish.message`:自定义 commit 消息，
-   `command.publish.registry`:指定要发布到的 npm 仓库地址，默认是`npmjs.org`
-   `command.bootstrap.ignore`:bootstrap 执行是要忽略的模块，可以是一个 globs 数组
-   `command.bootstrap.npmClientArgs`:一个字符串数组，当执行 lerna bootstrap 命令时，运行 npm install 的参数
-   `packages`:一个 globs 数组，指定作为 package 的位置

---

## 常用命令

### add

**`lerna add <package>[@version] [--dev] [--exact] [--peer]`**

添加本地或者远程 package 到本地 lerna 仓库，

**参数**

-   --dev
    将依赖作为`devDependencies`

-   --exact
    精确安装，比如使用（1.0.0）而不是（^1.0.0）

-   --peer
    将依赖作为`peerDependencies`

-   --registry <url>
    指定安装的镜像源

-   --no-bootstrap
    跳过`lerna bootstrap`命令链

**例子**

```bash
# 安装模块1到所有packages下以prefix-开头的模块
lerna add module1 packages/prefix-*

# 安装模块1作为模块2的依赖
lerna add module1 --scope=module2

# 安装模块1作为模块2的依赖,作为开发依赖
lerna add module1 --scope=module2 --dev

# 将模块1添加为所有模块的依赖（module1自身除外）
lerna add module1

# 安装@babel/core到所有模块中
lerna add @babel/core
```

### bootstrap

**`lerna bootstrap`**

启动命令，会做如下四件事

-   为每个包安装依赖
-   为本地相互依赖的模块创建软链接
-   为每个包运行 npm run prepublish
-   为每个包运行 npm run prepare

通过`--`给 npm 传递参数

```bash
lerna bootstrap -- --production --no-optional
```

或者

```json
{
    "npmClientArgs": ["--production", "--no-optional"]
}
```

**参数**

-   --hoist
    将依赖安装到根目录

-   --hoist[glob]
    排除按安装到根目录的模块`lerna boostrap --hoist --nohoist=@babel

-   --ignore-scripts
    不执行生命周期脚本命令， 比如 prepare

-   --registry <url>
    指定镜像源

-   --ignore-prepublish
    跳过 prepublish 生命周期

-   --npm-client <client>
    指定客户端例如`--npm-client=yarn`

-   --use-workspaces
    开启 yarn workSpaces

-   --no-ci
    lerna bootstrap 默认使用`npm ci`，此参数可以指定使用`npm install`

-   --ci
    指定使用`npm ci`

-   --force-local
    是 bootstrap 命令总是软链接本地依赖，不管匹配到的版本范围

### list

**`lerna list`**
列举当前 lerna 仓库所包含的包

**参数**

-   --json
    显示为 json 格式

-   --all
    显示所有的，包含 private

-   --long
    显示更多的信息

### changed

**`lerna changed`**
显示自上次 relase tag 后有修改的包，参数与 list 相同

**`lerna diff`**
显示自上次 relase tag 以来有修改的包的差异， 执行 git diff

### exec

**`lerna exec -- <command> [...args]`**
在每个包中执行一个任意的命令

例如：

```bash
lerna exec -- rm -rf ./node_modules
```

**参数**

-   --scope
    指定命令执行的作用域
    `lerna exec --scope my-compoent -- ls -la`
    只在 my-compoent 这个包下执行这个命令

-   --stream
    交叉并行输入结果
    `lerna exec --stream -- babel src -d lib`

-   --concurrency
    设置命令并行的并发量
    `lerna exec --concurrency 1 -- ls -la`

-   --no-bail
    默认 lerna exec 遇到错误就会退出执行，通过指定这个参数可以关闭这个行为，执行所有的包，不管 exit code

-   --no-prefix
    当使用--stream 或--parallel 时，禁止包名前缀

### run

**`lerna run <script> -- [..args]`**
执行每个 package 中的脚本

例如：

```bash
lerna run test
```

参数同 lerna exec

### clean

**`lerna clean`**
删除所有包下的 node_modules 目录

### link

**`lerna link`**
在当前的 Lerna repo 中将相互依赖的所有 Lerna 包软链接在一起

**参数**

-   --force-local
    强制软链接在一起，不管版本是否匹配

### create

**`lerna create <name> [loc]`**
创建一个 package
可以通过 --yes 使用默认选项

### info

**`lerna info`**

打印本地环境的信息
