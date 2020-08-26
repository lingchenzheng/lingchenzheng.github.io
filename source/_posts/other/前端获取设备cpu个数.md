---
title: 前端js获取cpu个数
category: 其他
tag: 解决方案
date: 2019/8/28
---

**简介**
前几天有个需求，就是前端要计算文件的 hash，如果文件比较小放在主线程完全没问题，但是如果同时选择多个大文件，就会阻塞主线，于是想着放到工作线程上计算 web worker。于是我就想起了线程池的方式，进入页面时实例好工作线程，放进数组。但是实例多少个好呢？于是我想起了 node 单机容灾，根据机器 cpu 个数来实例化应用的个数。那么前端怎么获取 cpu 个数呢？
经过查询，发现了 navigator.hardwareConcurrency 可以获取 cpu 个数
兼容性如下：

![](/images/other/前端获取设备cpu个数/1.png)

```javascript
let workers = []
let cpus = navigator.hardwareConcurrency || 4

for (let i = 0; i < cpus; i++) {
    let worker = new Worker()
    workers.push({
        worker,
        use: false
    })
}
```
