---
title: prettier格式化svg文件
category: 工具
date: 2018/12/20
---

prettier 本身没有格式化 svg 文件的功能，但是因为 svg 也是一种标签，可以看成是 HTML，因此可以如下设置。

`文件 => 首选项 => 设置 => setting.json`

```javascript
{
    "files.associations": {
        "*.svg": "html"
    }
}
```
