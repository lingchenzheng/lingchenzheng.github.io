---
title: apply与call函数实现
category: es
tag: js基础
date: 2018/9/16
---

**简介**

> 每个函数都有一个 apply 和 call 方法，用来函数执行时改变函数的`this` 。

**基本用法**

1. apply

```javascript
function fn(){}
fn.apply(obj[,array])
```

如上，obj 则为函数要指定的上下文`this`，后面是一个可选参数，是一个数组，数组的每个值对应参数列表。

2. call

```javascript
function fn(){}
fn.call(obj[, param1[,param2,[...params]]])
```

调用与 apply 一样，只是参数列表是依次传入。

**实现**

1. apply 实现

```javascript
Function.prototype.apply = function (context) {
    var args
    if (arguments.length > 1) {
        args = arguments[1]
    }
    context.fn = this
    var result = context.fn(...args)
    delete context.fn
    return result
}
```

2. call 实现

```javascript
Function.prototype.call = function (context) {
    var args = [...arguments].slice(1)
    context.fn = this
    var result = context.fn(...args)
    delete context.fn
    return result
}
```
