---
title: iframe介绍
category: web
---

#### 什么是 iframe?

iframe 是一个 html 标签，用来在当前页面加载并展示其他页面。

使用如下：

```html
<iframe src="./demo.html"></iframe>
```

#### iframe 的基本属性

-   src：要加载的页面的地址
-   height：iframe 的高度
-   width：iframe 的宽度
-   name：iframe 的名称，可以在父页面通过`window.frames[name]`获取 iframe 对象
-   scrolling：滚动模式
-   frameborder：是否显示边框 1 表示显示，0 表示不显示
-   sandbox：对 iframe 进行一些列限制

#### 获取 iframe 的 window 和 document 对象

```javascript
const iframe = document.getElementById('iframe')
const childWindow = iframe.contentWindow
const childDocument = iframe.contentDocument
```

注意：如果同域，则可以进行一些列操作，包括 dom，如果父子页面不同域，则只能进行页面跳转（childWindow.location.href = 'xxxx'）。

#### iframe 获取父级 window 对象

-   window.self：表示 iframe 自身
-   window.parent：表示其父级 window 对象
-   window.top：顶级 window 对象

**防止网页被被人引用**

```javascript
if (window.top !== window.self) {
    windown.top.location.href = window.self.location.href
}
```

#### iframe 的优缺点

##### 优点

-   可以轻松的集成其他页面，并展示出来
-   相较于 SPA 实现的微服务前端，有更好的资源隔离，比如样式文件
-   如果遇到加载缓慢的第三方内容如图标和广告，这些问题可以由 iframe 来解决

##### 缺点

-   会产生多个页面，难以管理
-   会阻塞主页面的 onload 事件（可以通过 js 动态设置 src 来解决）
-   安全问题
