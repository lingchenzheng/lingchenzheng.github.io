---
title: webpack导入svg图标
category: webpack
date: 2020/4/24
---

**简介**
svg 是一种基于 XML 格式的图像描述语音，它是矢量图，相比较与 canvas,放大时不会失真，相对于图像来说，因为它本就是文本形式存在，尺寸会更小。传统使用方式，我们会将 svg 复制到需要用到的地方，或者到处 import，这样代码不仅不美观，而且维护起来很麻烦，特别时图标多了的时候。接下来我们讲讲怎么以组件的方式使用 svg 图标（以 vue 为例 react 同理）。

**实现原理**
利用 svg 的 symbol 标签建立图标（不会显示出来），然后通过 use 标签引用，将对应 symbol 里的图形展示在页面

```html
<svg
    xmlns="http://www.w3.org/2000/svg"
    xmlns:xlink="http://www.w3.org/1999/xlink"
    style="position: absolute; width: 0; height: 0"
    aria-hidden="true"
    id="__SVG_SPRITE_NODE__"
>
    <symbol
        xmlns="http://www.w3.org/2000/svg"
        class="icon"
        viewBox="0 0 1024 1024"
        id="icon-back"
    >
        <path
            d="M724 218.3V141c0-6.7-7.7-10.4-12.9-6.3L260.3 486.8c-16.4 12.8-16.4 37.5 0 50.3l450.8 352.1c5.3 4.1 12.9 0.4 12.9-6.3v-77.3c0-4.9-2.3-9.6-6.1-12.6l-360-281 360-281.1c3.8-3 6.1-7.7 6.1-12.6z"
            p-id="7787"
            fill="#999999"
        ></path>
    </symbol>
    <symbol
        xmlns="http://www.w3.org/2000/svg"
        class="icon"
        viewBox="0 0 1024 1024"
        id="icon-close"
    >
        <path
            d="M794.843 794.843c-14.001 14.001-36.911 14.001-50.912 0L229.157 280.069c-14.001-14.001-14.001-36.911 0-50.912 14.001-14.001 36.911-14.001 50.912 0l514.774 514.774c14 14.001 14 36.911 0 50.912z"
            p-id="2135"
            fill="#4E5B75"
        ></path>
        <path
            d="M794.843 229.157c14.001 14.001 14.001 36.911 0 50.912L280.069 794.843c-14.001 14.001-36.911 14.001-50.912 0-14.001-14.001-14.001-36.911 0-50.912l514.774-514.774c14.001-14 36.911-14 50.912 0z"
            p-id="2136"
            fill="#4E5B75"
        ></path>
    </symbol>
</svg>
```

如上这段 svg 代码，里面包含两个图标，嵌入 html 页面是不会显示的，当我们需要使用某个图标时（id="icon-close"）为例，可以在 html 页面如下引用:

```html
<svg width="20px" height="20px" aria-hidden="true" class="svg-icon #icon-close">
    <use xlink:href="#icon-close"></use>
</svg>
```

这样思路就出来了，首先我们将所有 svg 文件打包成一个 svg 标签里，并且每个 symbol 标签都设置 id，这里我们可以通过 webpack 的 svg-sprite-lpader 来实现，如下:

```json
{
    "test": /\.svg$/,
    "loader": "svg-sprite-loader",
    "include": [resolve("src/icons/svgs")],
    "options": {
        "symbolId": "icon-[name]"
    }
}
```

一般我们会将作为图标使用的 svg 文件全部放在某个文件夹下，然后通过 include 处理这个文件下的 svg 文件，为了防止处理其他不要需要通过这个方式使用的 svg 被处理。

接下来我们要考虑怎么导入所有问 svg 文件，这里我们使用 webpack 的 require.context 来实现

```javascript
import Vue from 'vue'
import SvgIcon from '@/components/SvgIcon'
Vue.component('svg-icon', SvgIcon)

let req = require.context('./svgs', false, /\.svg$/)
let importAll = r => r.keys().map(r)
importAll(req)
```

首先我们将 SvgIcon 注册成全局组件，这样就不用到处 import 了，下面三行代码就是 require.context 动态引入 svgs 下的以.svg 结尾的文件，这里我们生成一个局部 webpack 上下文，即 svgs 文件夹路径，里面的文件导入基于这个上下文。最后在 index.js 引入这个文件即可。

最后来实现 SvgIcon 组件

```html
<template>
    <svg
        class="svg-icon"
        :width="w"
        :height="h"
        :class="iconName"
        aria-hidden="true"
        v-on="$listeners"
    >
        <use :xlink:href="iconName" />
    </svg>
</template>
<script>
    export default {
        name: 'SvgIcon',
        props: {
            icon: {
                type: String,
                required: true
            },
            w: {
                type: [Number, String],
                default: '20px'
            },
            h: {
                type: [Number, String],
                default: '20px'
            }
        },
        computed: {
            iconName() {
                return '#icon-' + this.icon
            }
        }
    }
</script>
<style lang="scss">
    .svg-icon {
        vertical-align: middle;
    }
</style>
```

然后在组件中使用

```html
<template>
    <div>
        <svg-icon icon="close" />
    </div>
</template>
```

如果文章对您有帮助请进入链接 (https://github.com/lingchenzheng/lingchenzheng.github.io) 给一个 star 吧 😄
