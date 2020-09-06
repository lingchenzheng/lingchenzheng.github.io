---
title: webpack代码分割
category: webpack
tag: 工具
date: 2020/3/26
---

##### 前言

代码分割是 webpack 一个很大的亮点，它允许我们将代码拆分进不同的 bundle 中，然后按需加载或者并行加载。

通常有 3 中方式进行拆分：

-   通过配置入口文件
-   通过`splitChunksPlugin`插件
-   通过`import`动态导入

##### entry

通过指定不同入口文件来进行代码分割，这是最简单直接的代码分割的方式，不过这个。

```javascript
const path = require('path')

module.exports = {
    mode: 'development',
    entry: {
        one: './src/one.js',
        two: './src/two.js'
    },
    output: {
        filename: '[name].bundle.js',
        path: path.resolve(__dirname, 'dist')
    }
}
```

打包结果如下

```
            Asset     Size   Chunks             Chunk Names
one.bundle.js  550 KiB  one  [emitted]  one
  two.bundle.js  550 KiB    two  [emitted]  two
Entrypoint two = two.bundle.js
Entrypoint one = one.bundle.js
```

不过这个方式有两个痛点：

-   如果两个入口文件都依赖同一个模块，那么这个模块会被分别打包进两个 bundle 中，代码重复。
-   它不够灵活，不能用于与核心应用程序逻辑动态分割代码。

阻止重复：

加入两个都依赖`loadsh`库，我们可以如下配置

```javascript
const path = require('path')

module.exports = {
    mode: 'development',
    entry: {
        one: {
            import: './src/one.js',
            dependOn: 'shared'
        },
        two: {
            import: './src/two.js',
            dependOn: 'shared'
        },
        shared: 'loadsh'
    },
    output: {
        filename: '[name].bundle.js',
        path: path.resolve(__dirname, 'dist')
    }
}
```

##### splitChunksPlugin

在 webpack4 之前是使用 CommonsChunkPlugin 来进行代码分割，在 webpack4 以后 CommonsChunkPlugin 被移除，推荐使用 splitChunksPlugin，它被 webapck 内置，在 optimization 配置项中配置。

在生产环境下，默认开启，默认配置如下：

```javascript
module.exports = {
    optimization: {
        splitChunks: {
            chunks: 'async',
            minSize: 20000,
            minRemainingSize: 0,
            maxSize: 0,
            minChunks: 1,
            maxAsyncRequests: 30,
            maxInitialRequests: 30,
            automaticNameDelimiter: '~',
            enforceSizeThreshold: 50000,
            cacheGroups: {
                defaultVendors: {
                    test: /[\\/]node_modules[\\/]/,
                    priority: -10
                },
                default: {
                    minChunks: 2,
                    priority: -20,
                    reuseExistingChunk: true
                }
            }
        }
    }
}
```

-   chunks

可选值为`async | initial | all`或者一个函数`function(chunk)`

来指定哪个 chunk 将被进行抽离。
`async`表示异步加载的模块，例如`import(xxx)`。
`initial`表示初始化加载的模块，`import xx from 'xx'`。
`all`表示所有模块，也就是以上两种都包含。

如果是函数，函数的返回值表示是否包含每个块

```javascript
module.exports = {
    optimization: {
        splitChunks: {
            chunks(chunk) {
                // exclude `my-excluded-chunk`
                return chunk.name !== 'my-excluded-chunk'
            }
        }
    }
}
```

_可以结合`HtmlWebpackPlugin`插件使用这个选项，它默认会插入所有的 bundles_

-   maxAsyncRequests

默认值：30
指定按需加载时最大的并行请求数

-   maxInitialRequests

默认值：30
每个入口点最大初始并行加载数

-   minChunks

默认值：1
chunks 被分割前至少被多少个模块依赖

-   minSize

默认值：20000
chunks 被分割前至少尺寸的大小（单位字节）

-   automaticNameDelimiter

默认值：~
指定生成名称的分隔符，例如`vendors~main.js`。

-   minRemainingSize(webpack5)

默认值：0
用来避免生成的 0 大小的 chunks，确保拆分后保留的块的最小大小高于限制。

-   maxSize

默认值：0
告诉 webpack 尝试将大于 maxSize 字节的块拆分为较小的部分，在 minSize 范围内。

**缓存组`cacheGroup`**
上面的配置项都可以在缓存组中覆盖。
定义 chunk 属于哪个缓存组，默认使用键名作为缓存组的名称，可以通过`name`指定

-   priority

默认值：0
控制缓存组的优先级，值越大，优先级越高，可以是负数

-   test

控制当下缓存组匹配的 chunks，如果不指定，默认匹配所有 chunks

-   filename

当 chunk 为同加载模块时，才允许覆盖文件名。

-   enforce

默认值：false
告诉 webpack 忽略`splitChunks.minSize, splitChunks.minChunks, splitChunks.maxAsyncRequests and splitChunks.maxInitialRequests` 选项，并且总是为当前缓存组创建 chunks。

-   reuseExistingChunk

如果当前 chunk 包含的模块已经从 main bundle 中分离除去，它将会被复用，而不是生成一个新的 chunk。

##### import 动态分割

webpack 将`import()`作为分割点。意思是，被请求的模块和它引用的所有子模块，会分离到一个单独的 chunk 中。import 方法依赖于 Promise，如果需要在低版本浏览器使用，需要进行 polyfill

```javascript
//a.js
import('./b.js').then(b => {
    //doSomething
})
```

import 规范不允许控制模块的名称或其他属性，因为 "chunks" 只是 webpack 中的一个概念，但是我们可以通过注释接收一些特殊的参数，而无须破坏规定。

-   webpackChunkName：手动指定模块的名称
-   webpackMode：指定 webpack 以什么模式解析动态导入

    -   lazy：默认值，为每个 import()导入的模块生成一个可延迟加载的 chunk
    -   lazy-once：生成 N 个可以延迟加载的 chunk，这个模式只能用于部分动态语句中有意义，比如 import(`./util/${tool}.js`)，webpack 会将 util 下的每个 js 文件分别打包成一个单独的 chunk。
    -   eager：这种模式不会生成额外的 chunk，所有模块都被当前 chunk 引入，不会有额外的网络请求。和静态导入相对比，在调用模块之前，该模块不会被执行。（静态导入第一回执行模块代码，ES Module 属于静态导入，commonjs 的 require 属于动态导入）
    -   weak：尝试加载模块，如果该模块函数已经以其他方式加载（即，另一个 chunk 导入过此模块，或包含模块的脚本被加载）。仍然会返回 Promise，但是只有在客户端上已经有该 chunk 时才成功解析。如果该模块不可用，Promise 将会是 rejected 状态，并且网络请求永远不会执行。当需要的 chunks 始终在（嵌入在页面中的）初始请求中手动提供，而不是在应用程序导航在最初没有提供的模块导入的情况触发，这对于通用渲染（SSR）是非常有用的。（这个还不没使用过）

例子

```javascript
//index.js
let util = 'a'
import(`./util/${util}.js`).then(res => {})

//util/a.js
export function add() {
    console.log('add')
}

//util/b.js
export const arr = [1, 2, 3]
```

打包后目录如下

![打包结果](/images/util/webpack/webpack代码分割/1.png)

我们可以看到，除了生成一个主 bundle（index.js），还生成了 1.js 和 2.js，分别对应 a.js 和 b.js 的 chunk。

**注意事项**

-   import()不支持完全动态语句，例如 import(util)，因为 webpack 至少需要一些文件的路径信息，而 util 可能是任何一个路径，webpack 不可能将所有模块都打包出来一个 chunk。

-   一般我们使用 import()动态导入的模块，就不要再在其他模块静态引入此模块了，因为 webpack 分割 chunk 时，模块会被打包进静态导入它的 chunk 中，这样会造成无法分割 chunk 或者代码重复打包。
