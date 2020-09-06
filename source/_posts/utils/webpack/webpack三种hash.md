---
title: hash,chunkhash,contenthash
category: webpack
date: 2020/4/12
---

-   hash

基于构建的，所有的 chunk 都使用相同的 hash，只要构建发生改变，hash 就发生改变，所有 chunk 的 hash 都会发生改变。

-   chunkhash

基于 webpack 的 entry point 的，每个入口点都有它自己的 hash。只有当特定的入口点发生变化时，相应的哈希值才会发生变化。

-   contenthash

Contenthash 是在 ExtractTextPlugin 中创建的特定类型的哈希，它是根据提取的内容而不是完全块内容来计算的。

使用：

-   在开发环境使用 hash
-   在生产环境，对 js 使用 chunkhash，对 css 应用 mini-css-extract-plugin 插件并使用 contenthash。
