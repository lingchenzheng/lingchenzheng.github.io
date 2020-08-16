---
title: 前端获取wma文件时长
category: 其他
tag: 解决方案
---

**简介**

> wma 是微软推出的一种音频存储格式，由于版权问题，目前浏览器的 audio/vedio 标签都不支持播放 wma 文件，由于项目需求需要前端读取本地音频时长等相关信息传到后端，所以只能另辟蹊径。

**思路**

> 读取二进制文件，根据文件头来解析文件属性信息，获取音频时长

下图为通过 windows media 工具来读取 wma 文件，分析文件内容组成
![](/images/other/前端js获取wma文件时长/1.png)

**经过多个文件分析对比，得出如下结果:**

1. 头对象由多个部分组成
2. 不同文件，头部大小不一样，头对象组成部分个数不一样，组成部分排列顺序也不一样
3. 文件属性内容固定，Object ID 都一样（文件时长相关信息在此）
4. 文件的前 30 个字节固定，16 个字节标识这是 wma 文件，然后 8 个字节存储头文件大小，4 字节存储头有几个组成部分，最后 2 个是保留字节。

**根据上面的结论，我们需要拿到头组成部分中的文件属性这个部分就可以解析出文件时长**

![文件属性](/images/other/前端js获取wma文件时长/2.png)

**实现步骤设计如下**

1. 截取 0-16 个字节，判断是否为 wma 文件(如果确定为 wma 文件可以省略)
2. 截取 16-24 个字节，获取头对象的大小
3. 根据第二步获取的头大小，截取头部数据
4. 拿到头部二进制数据，从 30 个字节往后，截取 16 个字节，根据这 16 个字节判断是否为文件属性部分
5. 如果是，根据图二的结构，设置偏移量，获取文件时长二进制数组
6. 如果不是，则再往后读取 8 个字节，获取当前部分大小
7. 通过第七步获取的大小设置偏移量，重复第四步

**简单实现代码如下**

```javascript
let input = document.getElementById('input')
input.addEventListener('change', e => {
    let file = e.target.files[0]
    file.slice(0, 16)
        .arrayBuffer()
        .then(buffer => {
            console.log(buffer)
        })
    return
    if (file && file instanceof Blob) {
        let reader = new FileReader()
        reader.onload = evt => {
            let { result } = evt.target
            console.log(result)
            // parseFile(result)
        }
        reader.readAsArrayBuffer(file.slice(0, 16))
    }
})
const FILE_PROPERTY_ID = [
    0xa1,
    0xdc,
    0xab,
    0x8c,
    0x47,
    0xa9,
    0xcf,
    0x11,
    0x8e,
    0xe4,
    0x00,
    0xc0,
    0x0c,
    0x20,
    0x53,
    0x65
]
const ID_BYTE_LEN = 16
const DURATION_OFFSET = 64
const START = 30
const BIT_LEN = 8
function parseFile(ab) {
    let uint8 = new Uint8Array(ab)
    let getFilePropertiesBytes = index => {
        let start = index
        let end = start + ID_BYTE_LEN
        let idBytes = uint8.slice(start, end)
        //id相等，则当前fram tag为文件属性
        if (idBytes.toString() === FILE_PROPERTY_ID.toString()) {
            start = start + DURATION_OFFSET
            end = start + BIT_LEN
            let durationBytes = uint8.slice(start, end)
            return toNum(durationBytes)
        } else {
            let countBytes = uint8.slice(end, end + BIT_LEN)
            let framLen = toNum(countBytes)
            start = start + framLen
            return getFilePropertiesBytes(start)
        }
    }

    let duration = getFilePropertiesBytes(START)
    console.log(duration)
}
function toNum(bytes) {
    let total = 0
    //采用的小端存储
    bytes.forEach((num, i) => {
        if (num > 0) {
            total += num * Math.pow(2, i * BIT_LEN)
        }
    })
    return total
}
```

如果文章对您有帮助请进入链接 (https://github.com/lingchenzheng/lingchenzheng.github.io) 给一个 star 吧 😄
