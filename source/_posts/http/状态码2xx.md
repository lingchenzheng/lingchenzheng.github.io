---
title: 状态码2xx，成功
category: http
tag: 网络
---

### 200

200 状态名称是 OK，表示请求成功并且数据正确响应。

### 201

201 状态名称是 Created，表示已创建。成功请求并创建了新的资源，该请求已被处理，而且有一个新的资源已经依据请求的需要而建立，且其 URI 已经随 Location 头信息返回。假如需要的资源无法及时建立的话，应当返回 '202 Accepted'。

### 202

202 状态名称是 Accepted，表示已接受请求，但是这个请求最终会也可能不会被执行，在异步操作的场合下，没有比发送这个状态码更方便的做法了。

例如：假如用户下一个订单，这个订单需要人工审核，人工审核需要时间，我们不能让这个请求一直等待，这个时候可以返回 202，表示请求已被接受，但是审核通不通过还不清楚。

### 203

203 状态名称是 Non-Authoritative Information，表示服务器已成功处理了请求，但返回的实体头部元信息不是在原始服务器上有效的确定集合，而是来自本地或者第三方的拷贝。

例如：包含资源的元数据可能导致原始服务器知道元信息的超集。使用此状态码不是必须的，而且只有在响应不使用此状态码便会返回 200 OK 的情况下才是合适的。

### 204

服务器成功处理了请求，但不需要返回任何实体内容。

### 205

服务器成功处理了请求，且没有返回任何内容。但是与 204 响应不同，返回此状态码的响应要求请求者重置文档视图。该响应主要是被用于接受用户输入后，立即重置表单，以便用户能够轻松地开始另一次输入。

### 206

206 状态码名称 Partial Content， 表示客户端执行了范围请求，而服务器成功执行了这部分 GET 请求，响应报文中包含有 content-range 指定范围的实体内容。类似于 FlashGet 或者迅雷这类的 HTTP 下载工具都是使用此类响应实现断点续传或者将一个大文档分解为多个下载段同时下载。
