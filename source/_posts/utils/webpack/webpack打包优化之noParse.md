---
title: webpack打包之noParse优化
category: webpack
tag: 工具
---

#### 介绍

noParse 是用来阻止 webpack 对三方库依赖的解析，这里并不是不解析这个三方库，而是不去处理这个三方库是否依赖了其他库，这样可以提高解析速度。比如我们使用 jquery 或者 loadsh，已经确定它们不会依赖其他库，这个时候可以配置 noParse 不去处理它们是否有依赖。

```javascript
module.exports = {
  module: {
    noParse: /jquery|lodash/
}
```

或者

```javascript
module.exports = {
    module: {
        noParse: (content) => /jquery|lodash/.test(content)
    }
}
```

这个时候当我们引入 jquery 的时候，webpack 会直接引入，不会去检查它时候有依赖其他库·
