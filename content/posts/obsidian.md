---
title: "Obsidian"
subtitle: ""
date: 2025-03-29T11:31:32+08:00
lastmod: 2025-03-29T11:31:32+08:00
draft: false
author: "Steven"
authorLink: "https://github.com/gusanzheng"
description: ""
license: ""
images: []

tags: ["obsidian"]
categories: ["tutorial"]

featuredImage: ""
featuredImagePreview: ""

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false
lightgallery: true
ruby: true
fraction: true
fontawesome: true
linkToMarkdown: true
rssFullText: false

toc:
  enable: true
  auto: true
  keepStatic: false
code:
  copy: true
  maxShownLines: 50
math:
  enable: false
  # ...
mapbox:
  # ...
share:
  enable: true
  # ...
comment:
  enable: true
  # ...
library:
  css:
    # someCSS = "some.css"
    # located in "assets/"
    # Or
    # someCSS = "https://cdn.example.com/some.css"
  js:
    # someJS = "some.js"
    # located in "assets/"
    # Or
    # someJS = "https://cdn.example.com/some.js"
seo:
  images: []
  # ...
---
**摘要**
简单使用obsidian做笔记。

**关键词**
`obsidian`

<!--more-->

## 推荐的插件

|        名称         |       解释        |
| :-----------------: | :---------------: |
| Clear Unused Images | 清理无引用的图片  |
|         Git         |    用来云同步     |
|   Number Headings   | 自动给heading标号 |


## 解决 obsidian mermaid 图片太大的问题

参考 [obsidian修改CSS解决mermaid图显示不全问题 - 博客](https://www.cnblogs.com/liqinglucky/p/17955750/obsidian-css)

mermaid.css
```css
.mermaid svg {
    width: 75%;
    height: auto;
}
div.mermaid {
    text-align: center;
}
```