---
title: webpack loader介绍
category: webpack
tag: 工具
---

### 介绍

> webpack 默认只能解析 js 和 json 类型的文件，loader 给 webpack 提供了强大的处理其他文件的能力。

### 分类

**按照调用顺序我们可以将 loader 分为以下几类**

#### inline loader(行内 loader)

使用`!`将 loader 与资源隔开

```javascript
import Styles from 'style-loader!css-loader?modules!./styles.css'
```

使用 inline loader 可以加上不同的前缀

-   加上`!`前缀，表示禁用所有的普通 loader

```javascript
import Styles from '!style-loader!css-loader?modules!./styles.css'
```

-   加上`!!`前缀，表示禁用所有的 loader，包括前置，普通，后置 loader

```javascript
import Styles from '!!style-loader!css-loader?modules!./styles.css'
```

-   加上`-!`前缀，表示禁用所有的 loader，包括前置，普通 loader，不包括后置 loader

```javascript
import Styles from '-!style-loader!css-loader?modules!./styles.css'
```

#### pre loader(前置 loader)，表示优先执行的 loader

```javascript
module.exports = {
    module: {
        rules: [
            {
                enforce: 'pre',
                loader: 'a-loader'
            }
        ]
    }
}
```

#### normal loader(正常 loader)

```javascript
module.exports = {
    module: {
        rules: [
            {
                loader: 'b-loader'
            }
        ]
    }
}
```

#### post loader(后置 loader)，表示最后执行的 loader

```javascript
module.exports = {
    module: {
        rules: [
            {
                enforce: 'post',
                loader: 'a-loader'
            }
        ]
    }
}
```

### loader 执行顺序

-   对于不同类型的 loader

执行顺序为：前置 loader -> 普通 loader -> 行内 loader -> 后置 loader

-   对于同一类型的 loader

执行顺序：从下至上，从右至左

如下：

```javascript
import { test } from 'e-loader!./util'
```

```javascript
module.exports = {
    module: {
        rules: [
            {
                test: /\.js$/,
                use: ['a-loader', 'c-loader']
            },
            {
                test: /\.js$/,
                loader: 'b-loader',
                enforce: 'pre'
            },
            {
                test: /\.js$/,
                loader: 'd-loader'
            }
        ]
    }
}
```

上面 loader 的执行顺序为 `b->d->c->a-e`
