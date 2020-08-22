---
title: Accept相关请求头部
category: http
tag: 网络
date: 2019/5/14
---

1. **Accept**

    - **类型**
      请求头部
    - **例子**
      Accept:text\/\*,image\/\*

    _注意：\*是一个特殊的通配符，\*\/\*表示接受所有媒体类型_

    - **作用**
      客户端通过 accept 首部来告诉服务器可以接受哪些媒体类型。

2. **Accept-Charset**

    - **类型**
      请求首部
    - **例子**
      Accept-Charset:iso-llatin-1
    - **作用**
      客户端通过 Accept-Charset 告诉服务器它可以接受哪些字符集或优选字符集。

3. **Accept-Encoding**

    - **类型**
      请求首部
    - **例子**
        - Accept-Encoding: deflate, gzip;q=1.0, \\\*;q=0.5
        - Accept-Encoding: compress
        - Accept-Encoding: gzip
    - **作用**
      客户端通过 Accept-Charset 告诉服务器它可以接受哪些字符集或优选字符集。

4. **Accept-Language**

    - **类型**
      请求首部
    - **例子**
        - Accept-Language: en
        - Accept-Language: en;q=0.7, en-gb;q=0.5
    - **作用**
      客户端通过 Accept-Language 告诉服务器它可以接受或优选哪些语言。

5. **Accept-Ranges**
    - **类型**
      响应首部
    - **例子**
        - Accept-Ranges: none
        - Accept-Ranges: bytes
    - **作用**
      与其他首部不同，Accept-Ranges 是一个响应首部，是服务器用来告诉客户端它们是否接受请求某个资源的请求范围。如果这个首部有值的话，这个值说明了服务器允许对指定资源的哪个范围进行访问。

**_关于 q(质量值)_**
http 协议中定义了质量值，允许客户端为每种偏好类型列出多种选项，并未每种选项定义一个优先级顺序。
q 的取值范围从 0.0-1.0（0.0 最低，1.0 最高）。

`Accept-Language: en;q=0.5, fr;q=1.0, tr;q=0.0`

如上面，表示客户端最愿意接受法语，其次是英语，最后是土耳其语。
