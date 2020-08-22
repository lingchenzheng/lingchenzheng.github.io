---
title: base64编码
category: 其他
date: 2020/3/17
---

##### 什么是 base64?

> Base64 是一组相似的二进制到文本（binary-to-text）的编码规则，使得二进制数据在解释成 radix-64 的表现形式后能够用 ASCII 字符串的格式表示出来。Base64 这个词出自一种 MIME 数据传输编码。
> Base64 编码普遍应用于需要通过被设计为处理文本数据的媒介上储存和传输二进制数据而需要编码该二进制数据的场景。这样是为了保证数据的完整并且不用在传输过程中修改这些数据。(引用自 [MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/WindowBase64/Base64_encoding_and_decoding))

##### base 原理

> base64 编码是将一个 8 位字节序列拆成 6 位的片段，并为每个 6 位的片段分配一个字符，这个字符是 base64 字母表中的一个，这 64 个字符包含了大小写字母，数字+和\/，还使用了特殊字符=。
> _注意：base64 编码用 8 位字符来表示其中的 6 位，所以编码后的结果比原数据打了约 33%_

**base64 字母表如下**

![base字母表](/images/other/base64/1.png)

**下面使用一个例子来说明 base64 是如何编码的**

在这里，我们有"Ow!"三个字符，它得到的 base64 编码结果是"T3ch"。实现流程如下：

-   字符串"Ow!"被拆成 3 个 8 位字节(0x4f,0x77,0x21)。
-   这 3 个字节构成了一个 24 位的二进制值(01001111 01110111 00100001)。
-   这些位被划分位一些 6 位的序列，010011，110111，011111，100001 。
-   每个 6 位表示了 0-63 位的一个字符。

![base64编码原理](/images/other/base64/2.png)

##### 填充

> 上面的例子中，3 个字节 24 位正好可以被 6 整分，但是如果是 2 个字节 16 位或者其他不能被整分的情况呢？
> 在这种情况下，就在末尾填充零位，使序列的长度正好是 24 的倍数(6 和 8 的最小公倍数)。
> 对已填充的二进制编码时，任何完全填充的 6 位（不包含原始数据中的位），都由特殊的第 65 个字符`=`来表示。如果填充的 6 位组是部分填充，就将填充位设置为 0 。

_简单例子_

![填充](/images/other/base64/3.png)

[参考 RFC2045](https://www.ietf.org/rfc/rfc2045.txt)

##### js 中编码解码

-   window.btoa() 编码
-   window.atob() 解码

```javascript
let encodedData = window.btoa('Hello, world') // 编码
let decodedData = window.atob(encodedData) // 解码
```

_对 Unicode 字符使用`window.btoa`报错解决方案：_

因为`window.btoa`只支持 ASCII 编码，可以借助 encodeURIComponent 和 decodeURIComponent 转义 Unicode 字符。

```javascript
let str = '好好学习'
windwo.btoa(encodeURIComponent(str))
```

##### Data URLs

> 前缀为`data:`的 url 协议，允许内容创建者向文档中嵌入小文件

-   语法

        `data:[<mediatype>][;base64],<data>`

    mediatype 是个 MIME 类型的字符串，例如 "image/jpeg" 表示 JPEG 图像文件。如果被省略，则默认值为 text/plain;charset=US-ASCII

-   例子

    -   data:text/plain;base64,SGVsbG8sIFdvcmxkIQ%3D%3D
        上一条示例的 base64 编码版本

    -   data:text/html,%3Ch1%3EHello%2C%20World!%3C%2Fh1%3E
        一个 HTML 文档源代码 \<h1\>Hello, World\<\/h1\>
