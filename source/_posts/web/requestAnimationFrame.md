---
title: requestAnimationFrame
category: web
date: 2019/5/22
---

##### 简介

requestAnimationFrame 是浏览器提供的一个接口，类似与 setTimeout，主要用途是按帧对网页进行重绘。

兼容性

![兼容性](/images/web/requestAnimationFrame/1.png)

##### 使用

requestAnimationFrame 接受一个回调函数，这个函数会在浏览器每帧刷新之前调用。

```javascript
function test() {
    requestAnimationFrame(test)
}
test()
```

取消，requestAnimationFrame 返回一个 long 型的 id 值，可以通过 cancelAnimationFrame 取消执行。

```javascript
let rafId
function test() {
    rafId = requestAnimationFrame(test)
}
test()
cancelAnimationFrame(rafId)
```

##### 兼容性写法

```javascript
window.requestAnimFrame = (function () {
    return (
        window.requestAnimationFrame ||
        window.webkitRequestAnimationFrame ||
        window.mozRequestAnimationFrame ||
        window.oRequestAnimationFrame ||
        window.msRequestAnimationFrame ||
        function (callback) {
            window.setTimeout(callback, 1000 / 60)
        }
    )
})()
```

##### 优点

-   requestAnimationFrame 利用显示器的刷新机制，保证动画在每帧渲染之前执行，使动画更加流畅。
-   使用这个 API，一旦页面不处于浏览器的当前标签，就会自动停止刷新，节省了 CPU 和 GPU 的消耗。
