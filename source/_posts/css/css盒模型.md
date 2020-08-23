---
title: CSS盒模型
category: CSS
date: 2018/5/24
---

**简介**

> html 元素像一个个盒子排列在文档流中，这些盒子由 content(内容),padding(内边距),border(边框),margin(外边距)组成，我们把这称作盒模型。

**分类**

1. 标准盒模型
   在标准盒模型中，元素的宽度，高度，也就是我们 css 设置 width 和 height 属性为 content 的宽高。
2. IE 盒模型
   在 IE 盒模型中，元素的宽度，高度，也就是我们 css 设置 width 和 height 属性为 content + padding + border 的宽高。

**设置**
元素默认为标准盒模型，可以通过 box-sizing 属性来设置
取值：`content-box | border-box| inherit`
