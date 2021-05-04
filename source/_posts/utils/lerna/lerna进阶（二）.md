---
title: lerna进阶（二）
category: lerna
tag: 工具
---

## 使用 workspace，提高磁盘利用率

> 在默认情况下，当我们使用`leran add`命令添加三方依赖是，包还是安装到每个`package`自己的目录下面，这样如果有重复的包，会在磁盘上生成多份重复的文件，大大降低了磁盘的利用率。
> lerna 支持`yarn workspace`的特性，这样将包都安装到根目录。

开启 workspace

-   1.修改`lerna.josn`文件

```json
{
    "useWorkspaces": true
}
```

-   2.修改根目录下的`package.json`文件

```json
{
    "workspaces": {
        "packages": ["packages/*"]
    }
}
```

`packages` 表示要包含的目录，是一个 `glob`表达式数组。

注意，lerna 是直接拿版本字符串对比的，只要完全相同才提升到根目录，如果存在不同版本的，还是会安装到 package 自身目录下。

## lerna publish 发布版本

### 使用

```shell
lerna publish              # 发布自上一个版本以来发生了变化的包
lerna publish from-git     # 发布当前提交中标记的包
lerna publish from-package # 发布注册表中没有最新版本的包
```

### 配置项

#### --canary

这个参数用来发布`alpha`版本

```shell
lerna publish --canary
# 1.0.0 => 1.0.1-alpha.0+${SHA} of packages changed since the previous commit
# a subsequent canary publish will yield 1.0.1-alpha.1+${SHA}, etc

lerna publish --canary --preid beta
# 1.0.0 => 1.0.1-beta.0+${SHA}

# The following are equivalent:
lerna publish --canary minor
lerna publish --canary preminor
# 1.0.0 => 1.1.0-alpha.0+${SHA}

```

#### --contents <dir>

这个参数用来指定发布的目录。

要发布的子目录。必定应用于所有包，且必须包含 package.json 文件。包的生命周期仍然在原来的叶子目录中运行。您应当使用其中的一个生命周期(prepare、prepublishOnly 或 prepack)来创建子目录等等。

```shell
# 发布每个由 Lerna 管理的叶子包的 dist 文件夹
lerna publish --contents dist
```

> 注意
> 您应该等到 postpublish 生命周期阶段(根目录或叶目录)才清理这个生成的子目录，因为生成的 package.json 是在包上传期间(在 postpack 之后)使用的。

#### --dist-tag <tag>

一般用来发布`beta`或预发布版本，帮助用户免于自动升级到预发布质量的代码。

拿 vue 来打比，为了避免用户在安装`install vue`直接安装到 vue3,所用 vue3 发布时使用`--dist-tag next`,所以当我们需要安装的是 vue3 时，需要`install vue@next`

```shell
lerna publish --dist-tag next
```

当使用`--dist-tag`时，如果不指定 tag,其默认是 latest。

### 发布作用域包

需要在每个 leaf 下的 `package.json` 如下配置

```json
{
    "publishConfig": {
        "access": "public"
    }
}
```
