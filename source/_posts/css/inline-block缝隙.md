---
title: inline-block元素产生缝隙
category: css
tag: 问题
---

### 产生缝隙的原因

元素被当成行内元素排版时，元素之间的空白符会被浏览器处理，原来 HTML 代码中的回车换行被转成一个空白符，在字体不为 0 的情况下，空白符占据一定宽度，所以 inline-block 的元素之间就出现了空隙。这些元素之间的间距会随着字体的大小而变化，当行内元素 font-size:16px 时，间距为 8px。

### 解决

-   将子元素放在同一行

-   将父元素 font-size 设置为 0，然后在子元素中设置字体大小

-   为 inline-block 元素添加样式 float:left

-   设置子元素的 margin 为负数

-   设置父元素，display:table 和 word-spacing 为 0
