---
title: 使用markdown绘制流程图
category: markdown
tag: 工具
date: 2019/7/2
---

##### 前言

上一篇文章我们讲了 markdown 的基本语法，这一节来说说 markdown 中怎么绘制流程图。

流程图并不属于 markdown 的标准语法，它有 markdown 引擎自己实现，不同引擎实现不同，比如 hexo 中默认就没有，必须安装插件。markdown 适用于简单一些的流程图，如果是非常复杂的流程图，建议还是使用专业的流程图工具。

先看一个简单的例子

**代码**

![](/images/markdown/markdown流程图/1.png)

**效果**

```flow

st=>start: 开始
op=>operation: 过程
e=>end: 结束
st->op
op->e

```

##### 使用

1. 使用前，我们必须定义每一步的元素

语法：

```
标识=>类型: 描述[:>url]
```

-   类型
    -   start 流程开始元素
    -   end 流程结束元素
    -   operation 一个操作元素
    -   condition 一个条件元素
    -   inputoutput 输入或输出元素
    -   subroutine 子程序

下面是一个简单例子

```
flow
st=>start: 开始
ask=>operation: 问题
know=>condition: 是否知道?
query=>operation: 百度:>https://www.baidu.com
answer=>inputoutput: 给出答案
e=>end: 结束

st->ask->know
know(yes)->answer
know(no)->query->answer
answer->e

```

```flow

st=>start: 开始
ask=>operation: 问题
know=>condition: 是否知道?
query=>operation: 百度:>https://www.baidu.com
answer=>inputoutput: 给出答案
e=>end: 结束

st->ask->know
know(yes)->answer
know(no)->query->answer
answer->e

```

##### hexo 集成 flow

hexo 默认并不支持解析流程图语法，必须安装插件`hexo-filter-flowchart`

-   安装

```shell
npm install --save hexo-filter-flowchart
```

-   配置

修改`_config.yml`文件，加上如下配置

```yml
flowchart:
    # raphael:   # optional, the source url of raphael.js
    # flowchart: # optional, the source url of flowchart.js
    options: #options used for'drawSVG'
```
