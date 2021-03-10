---
title: vscode配置jsx
category: 其他
tag: 工具
---

# 在项目根目录下创建 jsconfig.json 文件

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
