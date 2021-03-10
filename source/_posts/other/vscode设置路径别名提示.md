---
title: vscode设置路径别名提示
category: 其他
tag: 工具
date: 2021/3/10
---

### 在项目根目录下创建 jsconfig.json 文件

```json
{
    "compilerOptions": {
        "baseUrl": ".",
        "paths": {
            "@/*": ["./src/*"]
        }
    }
}
```
