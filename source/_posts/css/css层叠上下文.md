---
title: CSS 层叠上下文(Stacking Context)
category: css
date: 2018/6/24
---

**简介**

> 我们假定用户正面向（浏览器）视窗或网页，而 HTML 元素沿着其相对于用户的一条虚构的 z 轴排开，层叠上下文就是对这些 HTML 元素的一个三维构想。众 HTML 元素基于其元素属性按照优先级顺序占据这个空间。
> 简单说，就是 stacking context 决定这些元素的在 z 轴上的显示顺序，一般通过 z-index 来设置层叠顺序(stacking order)。
> _注意：元素 z-index 默认值为 auto，未定位的元素设置 z-index 无效_

> ![层叠上下文](/images/css/css层叠上下文/1.png)

**形成层叠上下文的条件**

-   文档根元素(html)
-   position 值为 absolute 或者 relative，且 z-index 值不为 auto 的元素
-   position 值为 fixed 或者 sticky 的元素
-   flex 布局中的子元素，且 z-index 不为 auto
-   grid 布局子元素，且 z-index 不为 auto
-   opacity(透明度)值小于 1 的元素
-   mix-blend-mode 属性值不为 normal 的元素
-   transform,filter,perspective,clip-path,mask \/ mask-image \/ mask-border 任一值不为 none 的元素
-   isolation 属性值为 isolate 的元素
-   -webkit-overflow-scrolling 属性值为 touch 的元素
-   will-change 值设定了任一属性而该属性在 non-initial 值时会创建层叠上下文的元素
-   contain 属性值为 layout、paint 或包含它们其中之一的合成值（比如 contain: strict、contain: content）的元素

**规则**

-   在同一层叠上下文，定位元素在出现非定位元素上面。

```html
<style>
    html,
    body {
        margin: 0;
        padding: 0;
    }
    .a {
        height: 100px;
        width: 100px;
        background-color: red;
        position: absolute;
    }
    .b {
        width: 100px;
        height: 100px;
        background: yellow;
    }
</style>
<div class="a"></div>
<div class="b"></div>
```

上面结果如下
![例子1](/images/css/css层叠上下文/2.png)

-   同一层叠上下文中，定位元素，z-index 值越大，越在上面
-   同一层叠上下文中，z-index 相同，按照元素顺序，越靠后越在上面
-   当一个元素创建了层叠上下文，这个元素将称为它子元素的根层叠上下文。

```html
<style>
    .a {
        height: 100px;
        width: 100px;
        background-color: red;
        position: absolute;
    }
    .b {
        width: 100px;
        height: 100px;
        background: yellow;
    }
</style>

<div class="a">
    <div class="b"></div>
</div>
```

如上代码，元素`a`会创建一个层叠上下文(可以称为局部上下文)，而它将作为`b`根层叠上下文

-   一个文档中可以有多个层叠上下文，所以层叠上下文是可以相互嵌套的。

-   新创建的层叠上下文的子级无法在不同堆栈上下文中的元素顶部呈现。即使它的 z-index 值设置得很大。

```html
<style>
    html,
    body {
        margin: 0;
        padding: 0;
    }
    .a {
        height: 50px;
        width: 200px;
        background-color: red;
        position: absolute;
        z-index: 10;
    }
    .b {
        width: 100px;
        height: 200px;
        background: yellow;
        position: absolute;
        z-index: 1;
    }
    .c {
        width: 100px;
        height: 100px;
        position: absolute;
        background-color: blue;
        z-index: 99999;
    }
</style>

<body>
    <div class="a"></div>
    <div class="b">
        <div class="c"></div>
    </div>
</body>
```

如上代码中，虽然 c 得 z-index 设置得很大，但它永远不可能出现在 a 得上面，因为它得根层叠上下文的层叠顺序比 a 低。

![例子](/images/css/css层叠上下文/3.png)

_注意：当我们把 b 的 z-index 去掉时，c 与 a 就处于同一层叠上下文了_

```html
<style>
    html,
    body {
        margin: 0;
        padding: 0;
    }
    .a {
        height: 50px;
        width: 200px;
        background-color: red;
        position: absolute;
        z-index: 10;
    }
    .b {
        width: 100px;
        height: 200px;
        background: yellow;
        position: absolute;
    }
    .c {
        width: 100px;
        height: 100px;
        position: absolute;
        background-color: blue;
        z-index: 99999;
    }
</style>

<body>
    <div class="a"></div>
    <div class="b">
        <div class="c"></div>
    </div>
</body>
```

结果如下

![例子](/images/css/css层叠上下文/4.png)

**总结**

-   设置某些 css 属性可以创建层叠上下文
-   给某个元素在本地堆栈上下文设置较大的值不会使该元素出现在其他堆栈上下文位于 DOM 中较高位置的元素之上。
-   z-index 索引仅在其自包含的堆栈上下文（全局或本地）中有意义
-   布局可以有许多堆叠上下文，彼此相邻，也可以在彼此内部
