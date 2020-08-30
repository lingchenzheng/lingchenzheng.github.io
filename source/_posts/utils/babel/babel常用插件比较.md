---
title: 关于@babel/polyfill，@babel/runtime，@babel/runtime-corejs3以及@bable/plugin-transform-runtime
category: babel
tag: 工具
date: 2019/12/12
---

##### 前言

对于标题中列举的几个 bable 插件，平时使用者，很多人都会感到迷惑，不知道怎么配置跟组合使用，这边文章主要是作者自己的理解，对这几个插件的介绍，以及后面会介绍不同的配置方案。

##### @babel/polyfill

我们知道，babel 是一个 js 编译器，将 javascript 新特新转换成浏览器能认识的代码，通过插件，我们能实现类似于箭头函数，拓展运算符之类的语法转换，但是一些新的 api 是无法转换的，比如 Map 对象，Object.assign 方法等等，这个时候我们就需要给环境中提供 polyfill，也就是打补丁的意思，注入这些 api，比如，浏览器不存在 Object.assign 方法，我们就在 Object 添加一个 assign 方法。这就是@babel/polyfill 所做的事。

使用方法，在项目入口文件引入

```javascript
import '@babel/polyfill'
```

`@babel/polyfill`相当于三个包的集合，它由`helpers`，`regenerator-runtime`和`core-js`三个包组成，不过 babel7.4 以后`@babel/polyfill`已经被废弃了，具体情况后面再说。

-   helpers

helpers 里面是一些辅助函数，在代码转换时需要用到，这些函数会带入生产环境。

例如：我们对`class`进行转换

源码

```javascript
class Person {}
```

转换后

```javascript
function _classCallCheck(instance, Constructor) {
    if (!(instance instanceof Constructor)) {
        throw new TypeError('Cannot call a class as a function')
    }
}

var Person = function Person() {
    _classCallCheck(this, Person)
}
```

`_classCallCheck`就是一个辅助函数。

-   regenerator-runtime

    这个包是用来转换 async 和 generator 函数的

源码

```javascript
function* test() {}
```

转换后

```javascript
require('regenerator-runtime/runtime')

var _marked = /*#__PURE__*/ regeneratorRuntime.mark(test)

function test() {
    return regeneratorRuntime.wrap(function test$(_context) {
        while (1) {
            switch ((_context.prev = _context.next)) {
                case 0:
                case 'end':
                    return _context.stop()
            }
        }
    }, _marked)
}
```

-   core-js

    上一篇[文章](/2019/12/08/utils/babel/core-js/)有介绍

##### @babel/runtime

`@babel/runtime` 包含了 helpers 函数，并且依赖于`regenerator-runtime`包，`@babel/runtime`的出现就是为了解决`@babel/polyfill`污染全局环境的问题。
使用`@babel/runtime`需要借助`@bable/plugin-transform-runtime`插件。

##### @bable/plugin-transform-runtime

@bable/plugin-transform-runtime 会做两件事

1. 在转码的过程中，babel 会加入辅助函数，也就是上文的 helpers，这些函数在每个用到辅助函数的文件都会重新存在一份，这些就造成了代码冗余，在上面的 helpers 介绍例子中可以看到，在文件中声明了一个\_classCallCheck 函数，`@bable/plugin-transform-runtime`的一个作用就是复用这些函数。

我们在 babel 配置文件加上如下配置

```json
{
    "plugins": [
        [
            "@bable/plugin-transform-runtime",
            {
                "helpers": true
            }
        ]
    ]
}
```

转换后

```javascript
var _interopRequireDefault = require('@babel/runtime/helpers/interopRequireDefault')

var _classCallCheck2 = _interopRequireDefault(
    require('@babel/runtime/helpers/classCallCheck')
)

var Person = function Person() {
    ;(0, _classCallCheck2['default'])(this, Person)
}
```

我们可以看到\_classCallCheck2 函数是引入的了，并不是直接加在文件里了。

2. 进行局部 polyfill，可以避免全局变量环境污染，这在写一个三方包时很有用。

    开启这个功能时需要安装`@babel/runtime-corejs3`包

##### @babel/runtime-corejs3

`@babel/runtime-corejs3`其实就是`@babel/runtime`于`core-js-pure`(core-js 文章中有介绍)的合成包。

下面以一个例子来说明

源码

```javascript
let map = new Map()
let set = new Set()
```

当我们通过`@babel/preset-env`引入`core-js`的 polyfill 时，转换后的结果如下

```javascript
require('core-js/modules/es.array.iterator')

require('core-js/modules/es.map')

require('core-js/modules/es.object.to-string')

require('core-js/modules/es.set')

require('core-js/modules/es.string.iterator')

require('core-js/modules/web.dom-collections.iterator')
var map = new Map()
var set = new Set()
```

我们可以看到，这些都是全局注入的

接下来换成`@bable/plugin-transform-runtime`来局部注入

```javascript
var _interopRequireDefault = require('@babel/runtime-corejs3/helpers/interopRequireDefault')

var _set = _interopRequireDefault(
    require('@babel/runtime-corejs3/core-js-stable/set')
)

var _map = _interopRequireDefault(
    require('@babel/runtime-corejs3/core-js-stable/map')
)

var map = new _map['default']()
var set = new _set['default']()
```

可以看到 Map 和 Set 都是局部引入。

##### @babel/preset-env

`@babel/preset-env`不仅对语法进行转换，还有一个强大的功能是它能够根据目标环境进行按需 polyfill，需要开启它的配置向`useBuiltIns`不为`false`。上面说到`@babel/polyfill`被废弃，就是因为它，它可以按需引入，代码量更小。
`@babel/preset-env`进行 polyfill 注入是注入全局的，需要手动安装`core-js`包。

```shell
npm install core-js@3.x
```

-   useBuiltIns 可以为三个值`entry|usage|false`

> 1.  entry 根据入口文件的引入转换成根据环境按需引入

输入

```javascript
import 'core-js/es/array'
import 'core-js/proposals/math-extensions'
```

输出

```javascript
import 'core-js/modules/es.array.unscopables.flat'
import 'core-js/modules/es.array.unscopables.flat-map'
import 'core-js/modules/esnext.math.clamp'
import 'core-js/modules/esnext.math.deg-per-rad'
import 'core-js/modules/esnext.math.degrees'
import 'core-js/modules/esnext.math.fscale'
import 'core-js/modules/esnext.math.rad-per-deg'
import 'core-js/modules/esnext.math.radians'
import 'core-js/modules/esnext.math.scale'
```

如果将 target 加上最小 ie10

```javascript
'use strict'

require('core-js/modules/es.array.concat')

require('core-js/modules/es.array.copy-within')

require('core-js/modules/es.array.every')

require('core-js/modules/es.array.fill')

require('core-js/modules/es.array.filter')

require('core-js/modules/es.array.find')

require('core-js/modules/es.array.find-index')

require('core-js/modules/es.array.flat')

require('core-js/modules/es.array.flat-map')

require('core-js/modules/es.array.for-each')

require('core-js/modules/es.array.from')

require('core-js/modules/es.array.includes')

require('core-js/modules/es.array.index-of')

require('core-js/modules/es.array.iterator')

require('core-js/modules/es.array.join')

require('core-js/modules/es.array.last-index-of')

require('core-js/modules/es.array.map')

require('core-js/modules/es.array.of')

require('core-js/modules/es.array.reduce')

require('core-js/modules/es.array.reduce-right')

require('core-js/modules/es.array.slice')

require('core-js/modules/es.array.some')

require('core-js/modules/es.array.species')

require('core-js/modules/es.array.splice')

require('core-js/modules/es.array.unscopables.flat')

require('core-js/modules/es.array.unscopables.flat-map')

require('core-js/modules/es.string.iterator')

require('core-js/modules/esnext.math.clamp')

require('core-js/modules/esnext.math.deg-per-rad')

require('core-js/modules/esnext.math.degrees')

require('core-js/modules/esnext.math.fscale')

require('core-js/modules/esnext.math.rad-per-deg')

require('core-js/modules/esnext.math.radians')

require('core-js/modules/esnext.math.scale')
```

> 2.  usage 根据环境自动按需导入 7.4 之前这个功能不稳定，慎用

以 Set 为例

```javascript
let set = new Set()
```

配置如下

```json
{
    "useBuiltIns": "usage",
    "corejs": {
        "version": 3
    },
    "targets": {
        "chrome": "70"
    }
}
```

转写结果没有变化，因为 chrome 支持 Set

```javascript
let set = new Set()
```

更改配置如下

```json
{
    "useBuiltIns": "usage",
    "corejs": {
        "version": 3
    },
    "targets": {
        "chrome": "70",
        "ie": "10"
    }
}
```

转写结果如下

```javascript
require('core-js/modules/es.array.iterator')

require('core-js/modules/es.object.to-string')

require('core-js/modules/es.set')

require('core-js/modules/es.string.iterator')

require('core-js/modules/web.dom-collections.iterator')

var set = new Set()
```

> 3.  `false`关闭 polyfill

-   corejs 指定 corejs 的版本，必须要

```json
{
    "corejs": 3
}
```

或者

```json
{
    "corejs": {
        "version": 3
    }
}
```

corejs 里有一个 proposals 属性，是否开启对草案阶段的属性 polyfill

```json
{
    "corejs": {
        "version": 3,
        "proposals": true
    }
}
```

例如：

```javascript
let str = 'asad'

str.matchAll(/\d/g)
```

这个默认是不处理的，如需处理，proposals 需要设置为 true

```javascript
require('core-js/modules/esnext.string.match-all')
let str = 'asad'
str.matchAll(/\d/g)
```

-   其他参数可以参考 bable 官网，这里就不多介绍。

##### 项目中如何配置

1. 如果我们是开发一个应用，我们应该使用@babel/preset-env 来进行 polyfill，因为它是按需进行注入的，可以减少代码尺寸，建议参数`useBuiltIns`设置为`usage`。因为是应用，所以不必担心全局环境污染。

这时我们需要安装如下插件

```shell
npm i @babel/preset-env @babel/plugin-transform-runtime -D
npm i core-js@3.6.5
```

`@babel/plugin-transform-runtime`需要，是因为我们需要复用辅助函数

配置如下

```javascript
module.exports = function (api) {
    api.cache(true)
    return {
        presets: [
            [
                '@babel/preset-env',
                {
                    useBuiltIns: 'usage',
                    corejs: {
                        version: 3,
                        proposals: true
                    },
                    targets: {
                        chrome: '70'
                    }
                }
            ]
        ],
        plugins: [
            [
                '@babel/plugin-transform-runtime',
                {
                    helpers: true,
                    corejs: false,
                    regenerator: false
                }
            ]
        ]
    }
}
```

2. 如果我们是在开发一个三方库，应当使用`@babel/plugin-transform-runtime`进行局部注入，避免别人使用我们的包造成全局污染。

这时我们需要安装如下插件

```shell
npm i @babel/preset-env @babel/plugin-transform-runtime -D
npm i @babel/runtime-corejs3 -S
```

配置如下

```javascript
module.exports = function (api) {
    api.cache(true)
    return {
        presets: ['@babel/preset-env'],
        plugins: [
            [
                '@babel/plugin-transform-runtime',
                {
                    helpers: true,
                    corejs: 3,
                    regenerator: true
                }
            ]
        ]
    }
}
```

##### 总结

-   全局于局部 polyfill 不能同时引入，只能开启一个，否则会报错。

-   为什么开发应用推荐使用`@babel/preset-env`的方式注入，因为它能根据环境按需引入，`@babel/plugin-transform-runtime`不能进行环境判断，即使浏览器支持也会引入。

-   插件的特性一直在变，应该关注并跟进，保证项目中好升级。
