---
title: 页面样式导入使用link与@import的区别
category: css
date: 2018/7/5
---

##### 前言

link 与@import 都可以用来导入 css 文件

区别：

-   link 是 html 标签，@import 是 css 提供的。
-   link 导入的 css 在页面加载时被加载，而@import 引用的 CSS 会等页面被加载完后再加载。
-   通过 js 操作 DOM,可以插入 link 标签来改变样式；由于 DOM 方法是基于文档的，无法使用@import 方式插入样式。
-   link 没有兼容性问题，@import 兼容到 IE5(实际上可以看成没有)
-   [权重问题](https://www.cnblogs.com/my--sunshine/p/6872224.html)
