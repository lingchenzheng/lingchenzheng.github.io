---
title: mac终端使用代理
category: 通用工具
tag: mac
---

#### 一般终端下载是不会主动使用代理，需要如下设置

```bash
# 配置所有
export all_proxy="http://127.0.0.1:7890"

# 配置http请求代理
export http_proxy="http://127.0.0.1:7890"

#配置https请求代码

export https_proxy="http://127.0.0.1:7890"

```

**注意：7890 为你本地代理的端口号，我的是 7890，根据你自己的来设置**
