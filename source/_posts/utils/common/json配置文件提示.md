---
title: json配置文件提示
category: 通用工具
tag: 开发效率
---

### 前言
> 作为一名开发者，在日常开发中少不了要使用各种工具，比如vscode,babel,typescript。而这些工具需要我们配置一些参数，一般通过json文件来配置，而这些配置往往会有需要参数，因为你经常不改动，我们很难记住这些属性，如果编辑器能够提供这些属性的提示那该多好。接下来将教大家如何来配置vscode这些文件的提示。

### JSON Schema

> 要想vsocde能够提示这些配置文件，我们需要借助json schema规范，它是用来描述json的结构，字段以及值的类型的元数据。

[目前最新规范](https://json-schema.org/draft/2019-09/release-notes.html)

假如有如下json结构

```json
{
    "person": {
        "name": "Jack",
        "age": 24
    },
    "num":27
}
```
那么它的json schema大概是这样

```json
{
    "title": "我的json schema",
    "definitions": {
        "person": {
            "type": "object",
            "required": ["name"],
            "properties": {
                "name": {
                    "type": "string"
                },
                "age": {
                    "type":"number"
                }
            }
        },
        "num":{
            "type": "number",
            "default": 27
        }
    }
}
```

### vscode配置

-   在setting.json中配置引入

```json
{
    "json.schemas": [
        {
            "fileMatch": [
                "/.babelrc"
            ],
            "url": "http://json.schemastore.org/babelrc"
        }
    ]
}
```
-  在setting.json中直接写入

```json
{
    "json.schemas": [
        {
            "fileMatch": [
                "/.myconfig"
            ],
            "schema": {
                "type": "object",
                "properties": {
                    "name": {
                    "type": "string",
                    "description": "The name of the entry"
                    }
                }
            }
        }
    ]
}

```

-   在json文件中直接引入

```json
{
    "$schmea": "https://json.schemastore.org/babelrc.json"
}
```

