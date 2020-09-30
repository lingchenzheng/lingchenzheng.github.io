---
title: webpack打包配置不处理依赖
category: webpack
tag: 工具
---

##### 前言

有时候我们打包非 web 环境包时，比如`node`或者`electron`的包，如果我们需要动态加载一些外部库，这里的动态加载不是指懒加载的意思，是指项目打包后，程序运行的时候再去加载的包。当然，对于`fs`这样的内部模块，webpack 自己已经做了处理，使用`externals`选项，所以不会将这些内置模块打包进 bundle 里。

那么，如果是我们自己定义的模块呢？我们如何做到不让 webpack 去解析这个依赖？而是运行时再去`require`。

这里我们也要用到`externals`选项，通过`externals`将`require`方法赋值给一个新的名。

```javascript
const webpack = require('webpack')
module.exports = {
    externals: {
        _require: 'require'
    },
    plugins: [
        new webpack.ProvidePlugin({
            _require: '_require'
        })
    ]
}
```

我们来解释一下上面的配置，首先`ProvidePlugin`是为了在每个文件注入代码`const _require = require('_require')`，这样我们就不用每处导入了。

而`externals`里`_require: 'require'`的作用是规定了如何处理引入`_require`模块，在这里表示将文件的内置`require`方法赋值给变量`_require`。

再来看个例子：

```javascript
//input.js
let A = _require('./a')
```

打包后

```javascript
//output.js部分
{
    './src/index.js': function (module, exports, __webpack_require__) {
        eval(
            '(function(_require) {let A = _require(\'./a\')}.call(this, __webpack_require__(/*! _require */ "_require")))\n\n//# sourceURL=webpack:///./src/index.js?'
        )
    },
    _require: function (module, exports) {
        eval(
            'module.exports = require;\n\n//# sourceURL=webpack:///external_%22require%22?'
        )
    }
}
```

可以看到，当我们使用`_require`去加载`./a`时并没有去处理依赖，而是运行时再去`require`。
