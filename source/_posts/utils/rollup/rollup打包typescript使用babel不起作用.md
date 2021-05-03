---
title: rollup打包typescript使用babel不起作用问题
category: rollup
tag: 工具
date: 2020/12/23
---

## 问题

> 在我们使用 `rollup` 打包 `typescript` 库时,`typescript`编译器本身只能对语法做转换，比如将箭头函数转换成`ES5`函数，对于新的 API 是没办法进行`polyfill`的，比如`Map`和`Set`等`ES6`的新 API 无法转换，这时我们需要通过`babel`的插件来进行`polyfill`。

在使用`rollup-plugin-typesctipt2`与`@rollup/plugin-babel`时，发现`babel`无法生效。

## 原因

> 由于 `babel` 不识别`ts`和`tsx`后缀文件

## 解决方法

### 1.将 babel 作用在输出文件上

```javascript
//使用 getBabelOutputPlugin
import { getBabelOutputPlugin } from '@rollup/plugin-babel'
import ts from 'rollup-plugin-typescript2'
export default {
    plugins: [
        ts(),
        getBabelOutputPlugin({
            plugins: [
                [
                    '@babel/plugin-transform-runtime',
                    {
                        helpers: true,
                        regenerator: true,
                        corejs: 3
                    }
                ]
            ]
        })
    ]
}
```

### 2.增加其文件拓展类型

```javascript
//使用 getBabelOutputPlugin
import { babel } from '@rollup/plugin-babel'
import ts from 'rollup-plugin-typescript2'
import { DEFAULT_EXTENSIONS } from '@babel/core'
export default {
    plugins: [
        ts(),
        getBabelOutputPlugin({
            plugins: [
                [
                    '@babel/plugin-transform-runtime',
                    {
                        helpers: true,
                        regenerator: true,
                        corejs: 3
                    }
                ],
                extensitions: [...DEFAULT_EXTENSIONS,'.ts','.tsx']
            ]
        })
    ]
}
```
