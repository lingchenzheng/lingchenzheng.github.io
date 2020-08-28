---
title: hexo 集成 mermaid
category: markdown
tag: 工具
date: 2019/7/3
---

**安装插件**

```
npm install hexo-filter-mermaid-diagram -S
```

**修改配置文件**
修改\_config.yml 文件

```yml
## mermaid diagram
mermaid: ## mermaid url https://github.com/knsv/mermaid
    enable: true
    version: '7.1.2'
    options:
        startOnload: true
```

**引入插件**
找到主题的模板文件，不同模板不一样（目的是在全局引入这个库，并初始化）

我这边使用的是 ejs

```ejs
 <% if (config.mermaid.enable) { %>
  <script src='https://unpkg.com/mermaid@<%= config.mermaid.version %>/dist/mermaid.min.js'></script>
  <script>
    if (window.mermaid) {
      mermaid.initialize({theme: 'forest'});
    }
  </script>
<% } %>
```
