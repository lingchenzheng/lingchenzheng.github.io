---
title: babel插件(plugin)与预设(preset)
category: babel
tag: 工具
date: 2019/12/3
---

##### 前言

上一篇文章讲了 babel 基础，我们知道 babel 本身不会对源码做任何操作，它对源码的转换都依赖与插件。这篇文章主要介绍一下对插件的理解。

我们可以将 babel 看成是工厂上的一条流水线，源码可以看成是要被加工的产品，插件就是流水线上的一道道工序，每道工序都对产品进行不同加工。如果没有工序，产品就相当于在流水先上走一趟，不做任何改变。所以插件才是对源码尽心转换的根本。

##### plugins 插件

**配置插件**

```javascript
module.exports = function () {
    return {
        plugins: ['pluginA', 'pluginB']
    }
}
```

**插件名称**

babel7 以后，babel 的包都以@babel 开头，这是 npm 包命名空间形式，表示这个包是 babel 的，当然只是建议这么命名。

1. `@babel/plugin-transform-arrow-functions` 箭头函数转换插件
2. `@babel/plugin-transform-block-scoping` 块级作用域转换插件
3. `etc` 其他

**插件分类**

babel 插件大致分为三类

1. syntax 语法类

    例如：`@babel/plugin-syntax-dynamic-import`

2. transform 转换类

    例如：`@babel/plugin-transform-async-to-generator`

3. proposal 转换类(指那些还没有进入标准中的新特性)

    例如：`@babel/plugin-proposal-dynamic-import`

-   syntax 类插件
    syntax 插件本身不会对代码做转换，它是用来识别语法的，如果某个转换类插件(transform 或 proposal)需要做某个语法转换，则会自动启动响应的 syntax 插件。这里以`@babel/plugin-syntax-dynamic-import`插件为例，它用来使用 ES6 动态导入`import`语法的，在 babel7 中其实它不需要安装和配置了，因为`@babel/preset-env`里包含了`@babel/plugin-proposal-dynamic-import`插件，而`@babel/plugin-proposal-dynamic-import`依赖`@babel/plugin-syntax-dynamic-import`，它会自动启用
    `@babel/plugin-syntax-dynamic-import`。所以我们是不需要自己安装和配置`@babel/plugin-syntax-dynamic-import`的。

-   transform 和 proposal 类插件
    这两个都是转换类插件，不同点是，transform 表示的是这个已经称为了特性 ES 规范的一部分，而 proposal 表示的是还在建议或者草案阶段的特性，随时有可能会被去掉。

##### presets 预设

我们在使用 babel 的时候，会去安装很多插件，比如转换箭头函数的，拓展运算符，对象展运算符等等一大堆插件，而且还要再配置文件里配置，很麻烦，而且由于一些相互配合的插件有调用顺序，每次手动配置容易出错。于是 babel 提供了 preset，preset 就是一个插件的集合。比如 babel 中最重要的一个 preset 插件，`@babel/preset-env`，包含了很多插件。

`@babel/preset-env`的相关依赖

```json
{
    "name": "@babel/preset-env",
    "version": "7.5.5",
    "dependencies": {
        "@babel/compat-data": "^7.11.0",
        "@babel/helper-compilation-targets": "^7.10.4",
        "@babel/helper-module-imports": "^7.10.4",
        "@babel/helper-plugin-utils": "^7.10.4",
        "@babel/plugin-proposal-async-generator-functions": "^7.10.4",
        "@babel/plugin-proposal-class-properties": "^7.10.4",
        "@babel/plugin-proposal-dynamic-import": "^7.10.4",
        "@babel/plugin-proposal-export-namespace-from": "^7.10.4",
        "@babel/plugin-proposal-json-strings": "^7.10.4",
        "@babel/plugin-proposal-logical-assignment-operators": "^7.11.0",
        "@babel/plugin-proposal-nullish-coalescing-operator": "^7.10.4",
        "@babel/plugin-proposal-numeric-separator": "^7.10.4",
        "@babel/plugin-proposal-object-rest-spread": "^7.11.0",
        "@babel/plugin-proposal-optional-catch-binding": "^7.10.4",
        "@babel/plugin-proposal-optional-chaining": "^7.11.0",
        "@babel/plugin-proposal-private-methods": "^7.10.4",
        "@babel/plugin-proposal-unicode-property-regex": "^7.10.4",
        "@babel/plugin-syntax-async-generators": "^7.8.0",
        "@babel/plugin-syntax-class-properties": "^7.10.4",
        "@babel/plugin-syntax-dynamic-import": "^7.8.0",
        "@babel/plugin-syntax-export-namespace-from": "^7.8.3",
        "@babel/plugin-syntax-json-strings": "^7.8.0",
        "@babel/plugin-syntax-logical-assignment-operators": "^7.10.4",
        "@babel/plugin-syntax-nullish-coalescing-operator": "^7.8.0",
        "@babel/plugin-syntax-numeric-separator": "^7.10.4",
        "@babel/plugin-syntax-object-rest-spread": "^7.8.0",
        "@babel/plugin-syntax-optional-catch-binding": "^7.8.0",
        "@babel/plugin-syntax-optional-chaining": "^7.8.0",
        "@babel/plugin-syntax-top-level-await": "^7.10.4",
        "@babel/plugin-transform-arrow-functions": "^7.10.4",
        "@babel/plugin-transform-async-to-generator": "^7.10.4",
        "@babel/plugin-transform-block-scoped-functions": "^7.10.4",
        "@babel/plugin-transform-block-scoping": "^7.10.4",
        "@babel/plugin-transform-classes": "^7.10.4",
        "@babel/plugin-transform-computed-properties": "^7.10.4",
        "@babel/plugin-transform-destructuring": "^7.10.4",
        "@babel/plugin-transform-dotall-regex": "^7.10.4",
        "@babel/plugin-transform-duplicate-keys": "^7.10.4",
        "@babel/plugin-transform-exponentiation-operator": "^7.10.4",
        "@babel/plugin-transform-for-of": "^7.10.4",
        "@babel/plugin-transform-function-name": "^7.10.4",
        "@babel/plugin-transform-literals": "^7.10.4",
        "@babel/plugin-transform-member-expression-literals": "^7.10.4",
        "@babel/plugin-transform-modules-amd": "^7.10.4",
        "@babel/plugin-transform-modules-commonjs": "^7.10.4",
        "@babel/plugin-transform-modules-systemjs": "^7.10.4",
        "@babel/plugin-transform-modules-umd": "^7.10.4",
        "@babel/plugin-transform-named-capturing-groups-regex": "^7.10.4",
        "@babel/plugin-transform-new-target": "^7.10.4",
        "@babel/plugin-transform-object-super": "^7.10.4",
        "@babel/plugin-transform-parameters": "^7.10.4",
        "@babel/plugin-transform-property-literals": "^7.10.4",
        "@babel/plugin-transform-regenerator": "^7.10.4",
        "@babel/plugin-transform-reserved-words": "^7.10.4",
        "@babel/plugin-transform-shorthand-properties": "^7.10.4",
        "@babel/plugin-transform-spread": "^7.11.0",
        "@babel/plugin-transform-sticky-regex": "^7.10.4",
        "@babel/plugin-transform-template-literals": "^7.10.4",
        "@babel/plugin-transform-typeof-symbol": "^7.10.4",
        "@babel/plugin-transform-unicode-escapes": "^7.10.4",
        "@babel/plugin-transform-unicode-regex": "^7.10.4",
        "@babel/preset-modules": "^0.1.3",
        "@babel/types": "^7.11.0",
        "browserslist": "^4.12.0",
        "core-js-compat": "^3.6.2",
        "invariant": "^2.2.2",
        "levenary": "^1.1.1",
        "semver": "^5.5.0"
    }
}
```

我们可以看到，`@babel/preset-env`这个插件里包含了很多其他插件，比如`@babel/plugin-transform-arrow-functions`，用来转换箭头函数的插件。

注意，并不是上面所有的插件都会用到，具体使用哪些插件会根据项目中配置的目标环境进行转换，preset 会根据[browserslist](https://github.com/browserslist/browserslist#query-composition)来确认目标环境，比如是否兼容 IE10 等等。

**如何自己定义一个 preset**

我们只需导出一份配置即可，可以是一个返回一个对象的函数

```javascript
module.exports = function () {
    return {
        plugins: ['pluginA', 'pluginB', 'pluginC']
    }
}
```

**preset 中可以包含其他 preset**

```javascript
module.exports = () => ({
    presets: [require('@babel/preset-env')],
    plugins: [
        [require('@babel/plugin-proposal-class-properties'), { loose: true }],
        require('@babel/plugin-proposal-object-rest-spread')
    ]
})
```

**preset 路径**

如果是`npm`上的包，可以直接输入名字

```json
{
    "presets": ["babel-preset-myPreset"]
}
```

如果是自定义的，可以使用相对路径或绝对路径

```json
{
    "presets": ["./myProject/myPreset"]
}
```

**执行顺序**

-   插件在 preset 前运行。
-   插件是按顺序执行。
-   preset 是倒序执行。

```javascript
module.exports = function () {
    return {
        presets: ['a', 'b', 'c']
    }
}
```

上面的配置，c 先执行然后是 b，最后执行 a

```javascript
module.exports = function () {
    return {
        plugins: ['pluginA', 'pluginB', 'pluginC']
    }
}
```

先执行 pluginA，在执行 pluginB，最后执行 pluginC
