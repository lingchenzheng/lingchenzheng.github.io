---
title: core-js
category: babel
tag: 工具
date: 2019/12/8
---

**core-js 是什么?**

-   它是 javascript 标准库的 polyfill，它支持

    -   最新的 ECMAScript 标准
    -   ECMAScript 标准库提案
    -   一些 [WHATWG](https://en.wikipedia.org/wiki/WHATWG) / W3C 标准（跨平台或者 ECMAScript 相关）

-   它最大限度的模块化：你能仅仅加载你想要使用的功能

-   它能够不污染全局命名空间

-   它和 babel 紧密集成：这能够优化 core-js 的导入

**core-js2 与 core-js3**

作者写这篇文章的时候，已经到`core-js@3.6.5`了，相比较与`core-js@2.x`，有如下改变：

-   引进了一些新功能：

    -   增加支持 ECMAScript 2015 引入的两个知名标志 [`@@isConcatSpreadable`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/isConcatSpreadable) 和 [`@@species`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/species)，给所有使用他们的方法。
    -   增加来自 ECMAScript 2018 的 [`Array.prototype.flat`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/flat) 和 [`Array.prototype.flatMap`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/flatMap)（ `core-js@2` 针对 `Array.prototype.flatten` 这个老版本的提案提供了补丁）。
    -   增加来自 ECMAScript 2019 的 [`Object.fromEntries`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/fromEntries) 方法
    -   增加来自 ECMAScript 2019 的 [`Symbol.prototype.description`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/description) 访问器

-   一些在 ES2016-ES2019 中作为提案被接受且已经使用很长时间的功能，现在被标记为稳定：

    -   [`Array.prototype.includes`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/includes) 和 [`TypedArray.prototype.includes`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypedArray/includes) 方法（ ESMAScript 2016 ）
    -   [`Object.values`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/values) 和 [`Object.entries`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/entries) 方法( ECMAScript 2017 )
    -   [`Object.getOwnPropertyDescriptors`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyDescriptors) 方法 ( ECMAScript 2017 )
    -   [`String.prototype.padStart`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/padStart) 和 [`String.prototype.padEnd`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/padEnd) 方法（ ECMAScript 2017 ）
    -   [`Promise.prototype.finally`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/finally) 方法（ ECMAScript 2018 ）
    -   [`Symbol.asyncIterator`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/asyncIterator) 知名标志（ ECMAScript 2018 ）
    -   [`Object.prototype.__define(Getter|Setter)__`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/__defineGetter__) 和 [`Object.prototype.__lookup(Getter|Setter)__`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/__lookupGetter__) 方法（ ECMAScript 2018 ）

-   修复了针对浏览器的许多问题，例如，Safari 12.0 Array.prototype.reverse bug 已经被修复了。

除了上文提到的支持内容，`core-js@3` 现在还支持下面的 ECMAScript 提案：

-   [`globalThis`](https://github.com/tc39/proposal-global) stage 3（ 现在是 stage 4 ）的提案 - 之前，已经有了 `global` 和 `System.global`
-   [`Promise.allSettled`](https://github.com/tc39/proposal-promise-allSettled) stage 2（ 现在是 stage 4 ）提案
-   [新 `Set` 方法](https://github.com/tc39/proposal-set-methods) stage 2 提案：
    -   Set.prototype.difference
    -   Set.prototype.intersection
    -   Set.prototype.isDisjoinFrom
    -   Set.prototype.isSubsetOf
    -   Set.prototype.isSupersetOf
    -   Set.prototype.symmetricDifference
    -   Set.prototype.union
-   [新 collections 方法](https://github.com/tc39/proposal-collection-methods) stage 1 提案，包函许多新的有用的方法：
    -   Map.groupBy
    -   Map.keyBy
    -   Map.prototype.deleteAll
    -   Map.prototype.every
    -   Map.prototype.filter
    -   Map.prototype.find
    -   Map.prototype.findKey
    -   Map.prototype.includes
    -   Map.prototype.keyOf
    -   Map.prototype.mapKeys
    -   Map.prototype.mapValues
    -   Map.prototype.merge
    -   Map.prototype.reduce
    -   Map.prototype.some
    -   Map.prototype.update
    -   Set.prototype.addAll
    -   Set.prototype.deleteAll
    -   Set.prototype.every
    -   Set.prototype.filter
    -   Set.prototype.find
    -   Set.prototype.join
    -   Set.prototype.map
    -   Set.prototype.reduce
    -   Set.prototype.some
    -   WeakMap.prototype.deleteAll
    -   WeakSet.prototype.addAll
    -   WeakSet.prototype.deleteAll
-   [`String.prototype.replaceAll`](https://github.com/tc39/proposal-string-replace-all) stage 1（ 现在是 stage 3 ） 提案
-   [`String.prototype.codePoints`](https://github.com/tc39/proposal-string-prototype-codepoints) stage 1 提案
-   [`Array.prototype.last(Item|Index)`](https://github.com/tc39-transfer/proposal-array-last) stage 1 提案
-   [`compositeKey` 和 `compositeSymbol` 方法](https://github.com/bmeck/proposal-richer-keys/tree/master/compositeKey) stage 1 提案
-   [`Number.fromString`](https://github.com/tc39/proposal-number-fromstring) stage 1 提案
-   [`Math.seededPRNG`](https://github.com/tc39/proposal-seeded-random) stage 1 提案
-   [`Promise.any` (合并的错误)](https://github.com/tc39/proposal-promise-any) stage 0（ 现在是 stage 3 ）提案

一些提案的变化很大，`core-js` 也将相应的更新：

-   [`String.prototype.matchAll`](https://github.com/tc39/proposal-string-matchall) stage 3 提案
-   [Observable](https://github.com/tc39/proposal-observable) stage 1 提案

web 标准

最重要的一个是 [`URL`](https://developer.mozilla.org/en-US/docs/Web/API/URL) 和 [`URLSearchParams`](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams)。他是[最受欢迎的功能请求之一](https://github.com/zloirock/core-js/issues/117)。增加 `URL` 和 `URLSearchParams`，并保证他们最大限度的符合规范，保持源代码足够紧凑来支撑任何环境是 `core-js@3` 开发中[最困难的任务之一](https://github.com/zloirock/core-js/pull/454/files)。

`core-js@3` 包函在 JavaScript 中创建微任务（ microtask ）的标准方法：[`queueMicrotask`](https://html.spec.whatwg.org/multipage/timers-and-user-prompts.html#microtask-queuing) 。`core-js@2` 提供了 `asap` 函数，提供了同样功能的老的提案。`queueMicrotask` 被定义在 HTML 标准中，它已经能够在现代浏览器比如 Chromium 或者 NodeJS 中使用。

另一个受欢迎的功能请求是支持 [DOM 集合的 `.forEach` 方法](https://developer.mozilla.org/en-US/docs/Web/API/NodeList/forEach)。由于 `core-js` 已经针对 DOM 集合迭代器做了 polyfill，为什么不给 `节点列表` 和 [`DOMTokenList`](https://developer.mozilla.org/zh-CN/docs/Web/API/DOMTokenList) 也增加 `.forEach` 呢？

移除过时的功能

-   `Reflect.enumrate` 因为他已经从标准中移除了
-   `System.global` 和 `global` 现在他们已经被 `globalThis` 代替
-   `Array.prototype.flatten` 现在被 `Array.prototype.flat` 代替
-   `asap` 被 `queueMicrotask` 代替
-   `Error.isError` 被撤销很长时间了
-   `RegExp.escape` 很久之前被拒绝了
-   `Map.prototype.toJSON` 和 `Set.prototype.toJSON` 也是很久前被拒绝了
-   不必要并且被错误添加的迭代器方法：`CSSRuleList`，`MediaList`，`StyleSheetList`。

**包类型**
core-js 提供了三种类型的包：

```shell
// 定义了全局polyfill(~500KB，压缩并且 gzipped 处理后 40KB)
npm install --save core-js@3.x
// 提供了不污染全局变量的 polyfills。它和 core-js@2 中的 core-js/library 相当。（~440KB）
npm install --save core-js-pure@3.x
// 定义了全局填充的打包版本，可以使用script标签引入
npm install --save core-js-bundle@3.x
```

第一个是全局包，会全局引入所有的 polyfill 属性，第二个`pure`表示纯的，不会有副作用的意思，是局部 polyfill，不会造成全局环境污染。第三个相当于将第一个打包好，可以使用 script 标签加载。

第一个版本使用

```javascript
import 'core-js' // <- at the top of your entry point

Array.from(new Set([1, 2, 3, 2, 1])) // => [1, 2, 3]
;[1, [2, 3], [4, [5]]].flat(2) // => [1, 2, 3, 4, 5]
Promise.resolve(32).then(x => console.log(x)) // => 32
```

当然第一个也可以按需导入

```javascript
import 'core-js/features/array/from' // <- at the top of your entry point
import 'core-js/features/array/flat' // <- at the top of your entry point
import 'core-js/features/set' // <- at the top of your entry point
import 'core-js/features/promise' // <- at the top of your entry point

Array.from(new Set([1, 2, 3, 2, 1])) // => [1, 2, 3]
;[1, [2, 3], [4, [5]]].flat(2) // => [1, 2, 3, 4, 5]
Promise.resolve(32).then(x => console.log(x)) // => 32
```

第二个版本使用

```javascript
import from from 'core-js-pure/features/array/from'
import flat from 'core-js-pure/features/array/flat'
import Set from 'core-js-pure/features/set'
import Promise from 'core-js-pure/features/promise'

from(new Set([1, 2, 3, 2, 1])) // => [1, 2, 3]
flat([1, [2, 3], [4, [5]]], 2) // => [1, 2, 3, 4, 5]
Promise.resolve(32).then(x => console.log(x)) // => 32
```

**core-js 入口使用案例**

```javascript
// 使用 `core-js` 全部功能打补丁：
import 'core-js'
// 仅仅使用稳定的 `core-js` 功能 - ES 和 web 标准：
import 'core-js/stable'
// 仅仅使用稳定的 ES 功能
import 'core-js/es'

// 如果你想用 `Set` 的补丁
// 所有 `Set`- ES 提案中，相关的功能：
import 'core-js/features/set'
// 稳定的 `Set` ES 功能和来自web标准的功能
// （DOM 集合迭代器）
import 'core-js/stable/set'
// 只有 `Set` 所需的稳定的 ES 功能
import 'core-js/es/set'
// 与上面一致，但不会污染全局命名空间
import Set from 'core-js-pure/features/set'
import Set from 'core-js-pure/stable/set'
import Set from 'core-js-pure/es/set'

// 仅仅为需要的方法打补丁
import 'core-js/feature/set/intersection'
import 'core-js/stable/queque-microtask'
import 'core-js/es/array/from'

// 为 reflect metadata 提案打补丁
import 'core-js/proposals/reflect-metadata'
// 为所有 stage 2+ 的提案打补丁
import 'core-js/stage/2'
```

##### 与 babel 集成

-   **@babel/polyfill**

一个包裹的包，里面仅仅包含 core-js 稳定版的引入（在 Babel 6 中也包含提案）和 `regenerator-runtime/runtime`，用来转译 generators 和 async 函数。这个包没有提供从 core-js@2 到 core-js@3 平滑升级路径：因为这个原因，决定弃用 @babel/polyfill 代之以分别引入需要的 core-js 和 regenerator-runtime 。

使用

```javascript
import '@babel/polyfill'
```

现在直接安装`npm i --save core-js@3.6.5 regenerator-runtime`

```javascript
import 'core-js/stable'
import 'regenerator-runtime/runtime'
```

-   **@babel/preset-env**

`@babel/preset-env` 有两种不同的模式，通过 useBuiltIns 选项：entry 和 usage 优化 core-js 的导入。

由于现在 `@babel/preset-env` 支持 `core-js@2` 和 `core-js@3`，因此 useBuiltIns 需要新的选项 -- corejs，这个选项用来定义使用 core-js 的版本（corejs: 2 或者 corejs: 3）。如果没有设置，corejs: 2 是默认值并且会有警告提示。

为了使 babel 支持将来的次要版本中引入的 core-js 的新功能，你可以在项目中定义明确的次要版本号。例如，你想使用 `core-js@3.1` 使用这个版本的新特性，你可以设置 corejs 选项为 3.1：corejs: '3.1' 或者 corejs: {version: '3.1'}。

`@babel/preset-env`的一个好处是可以根据目标环境来决定是否要引入相关特性的 polyfill，`@babel/preset-env`的数据来源于 compat-table，但是仍然有一些限制

-   它包含的数据仅仅关于 ECMAScript 特性和提案，和 web 平台特性例如 setImmediate 或者 DOM 集合迭代器没有关系。所以直到现在，`@babel/preset-env` 仍然通过 core-js 添加全部的 web 平台特性即使他们已经支持了。

-   它他不包含任何浏览器（甚至是严重的）bug 信息：例如，上文提到的在 Safari 12 中 Array#reverse，但是 compat-table 并没有将它标记为不支持。另一方面，core-js 已经修复了这个错误实现，但是因为 compat-table 关系，并不能使用它。

-   它仅包函一些基础的、幼稚的测试，没有检查功能在真实环境下是否可以正常工作。例如，老版本 Safari 的破坏的迭代器没有 .next 方法，但是 compat-table 表明 Safari 支持，因为它用 typeof 方法检测迭代器方法返回了 "function"。一些像 typed arrays 的功能几乎没有覆盖。

-   compat-table 不是为了向工具提供数据而设计的(因此 core-js 作者创建了 core-js-compat：它提供了对于不同浏览器 core-js 模块的必要性数据。当使用 core-js@3 时，@babel/preset-env 将使用新的包取代 compat-table)。

-   **@babel/runtime**

    `@babel/preset-env`会进行全局引入，这样会污染全局环境。 `@babel/transform-runtime` 现在通过 core-js-pure（core-js 的一个版本，不会污染全局变量） 注入 polyfills。

    通过将`@babel/transform-runtime`设置 corejs: 3 选项和创建 `@babel/runtime-corejs3` 包，已经将 `core-js@3`和`@babel/runtime`集成在一起。

##### 总结

-   core-js 可以提供那些 polyfill 以及它们于 features 的关系，可以在[core-js#features](https://github.com/zloirock/core-js#features)查询

-   corej-js3 相比较与 core-js2，修复了许多问题，应该尽量使用 core-js3。同时 babel 应当升级到 7.4 及以后。

-   如果在每个细节中都无法按照规范实现某个功能，则 core-js 会向 polyfill 添加.sham 属性。 例如，在 IE11 中，Symbol.sham 为 true。

-   core-js-compat 是 core-js 提供的一个类似 compat-table 一样的库，基于它，能知道 core-js 的每个 polyfill 在不同的环境里面的兼容情况。 core-js-builder 就是依赖于 core-js-compat 实现的。 另外在@babel/preset-env 里面，如果使用了 core-js@3，也会使用 core-js-compat，而不是 compact-table。

-   compat-table 的初衷不是为了给其它库提供数据支持。core-js 的作者也是 compat-table 项目的参与者之一，他知道其它一些 compat-table 项目的参与者是反对给其它库提供数据支持的，与是 core-js 专门写了 core-js-compat，目的就是为给@babel/preset-env 提供数据

_参考：[core-js](https://github.com/zloirock/core-js/blob/master/docs/zh_CN/2019-03-19-core-js-3-babel-and-a-look-into-the-future.md)_
