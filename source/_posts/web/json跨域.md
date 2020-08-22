---
title: jsonp跨域
category: web
tag: 解决方案
date: 2019/6/28
---

**前言**

> 首先，jsonp 的出现是为了解决跨域请求的。跨域是由于浏览器的同源策源造成的，同源策略限制了从同一个源加载的文档或脚本如何与来自另一个源的资源进行交互。这是一个用于隔离潜在恶意文件的重要安全机制。同源是指协议、域名和端口号相同，如果三者有一不同则出现跨域。

**实现机制**

1. 虽然 ajax 受到同源策略的限制，但是 script 标签的 src 属性不会受到同源策略的限制，可以任意获取服务器上的脚本并执行，这是 jsonp 实现的前提。

2. 实现 jsonp 的思路是，在客户端口动态创建 script 标签，设置其 src 为服务端接口地址，将脚本插入文档，通过 script 向服务端发送请求,在这之前，首先在全局对象上挂载一个全局函数作为 jsonp 的回调函数，也就是服务端口将返回这个函数的调用作为 script 标签的内容，并将数据作为函数的参数返回，数据返回后再从文档删除此标签。

**代码**

```javascript
function jsonp(url, params, cb) {
    return new Promise((res, rej) => {
        let sp = document.createElement('script')
        window[cb] = function (data) {
            res(data)
            document.body.removeChild(sp)
        }
        let arr = []
        for (let key in params) {
            arr.push(`${key}=${params[key]}`)
        }
        let query = arr.join('&')
        sp.src = `${url}?callback=${cb}&query`
        document.body.appendChild(sp)
    })
}
```
