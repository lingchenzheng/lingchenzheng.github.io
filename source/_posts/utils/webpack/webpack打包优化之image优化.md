---
title: webpack打包image优化
category: webpack
tag: 工具
date: 2019/9/25
---

##### 前言

在一个web应用中，可能图片的大小就占了整个应用的一半，所以我们需要对图片进行优化，大概可以分为以下两种情况：

> - 有时候我们会使用一些小的图片icon，虽然这些icon尺寸很小，但是浏览器加载每个icon都会创建一个http请求，创建http请求是昂贵的，所以这明显是不合理的。
> - 对与比较大的图片，一个是比较占用带宽，另一个是加载慢，影响用户体验。

##### 优化方案

> 针对第一种情况，我们可以根据图片的大小，将较小的图片转换成base64，然后以data url的方式内嵌在css或者js中，这样在加载css或js的时候一起把图片加载，不需要额外的http请求。

1. url-loader
```javascript
// webpack.config.js
module.exports = {
  module: {
    rules: [
      {
        test: /\.(jpe?g|png|gif)$/,
        loader: 'url-loader',
        options: {
          // 小于10240字节的图片将被转换成base64嵌入js代码中
          limit: 20 * 1024,
        },
      },
    ],
  }
}
```
*注意：图片转换成base64后，大小会比原来大1/3左右，所以应该根据图片大小来斟酌使用，同时应该开启gzip压缩*

2. svg-url-loader
> svg图片也可以使用url-loader来处理成base64，但是由于svg本身就是一个纯文本文件，所以采用svg-url-loader使用URL encoding处理尺寸会更小。

```javascript
    // webpack.config.js
module.exports = {
  module: {
    rules: [
      {
        test: /\.svg$/,
        loader: 'svg-url-loader',
        options: {
          // 小于10240的进行URL编码
          limit: 20 * 1024,
          // 移除引号
          // (they’re unnecessary in most cases)
          noquotes: true,
        },
      },
    ],
  },
}
```
3. image-webpack-loader
> image-webpack-loader可以支持JPG, PNG, GIF and SVG 图片的压缩，因为它仅仅是压缩作用，不会将图片嵌入应用，因此我们需要与url-loader和svg-url-loader一起使用，为了不需要在每个loader里复制一遍image-webpack-loader的配置，我们可单独将其配置在一个规则中，并使用enfore:"pre"让它最先执行。

```javascript
 // webpack.config.js
module.exports = {
  module: {
    rules: [
      {
        test: /\.(jpe?g|png|gif|svg)$/,
        loader: 'image-webpack-loader',
        // This will apply the loader before the other ones
        enforce: 'pre',
      },
    ],
  },
}
```

如果文章对您有帮助请进入链接 (https://github.com/lingchenzheng/lingchenzheng.github.io) 给一个 star 吧 😄