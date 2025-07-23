---
title: "Obsidian"
subtitle: ""
date: 2025-03-29T11:31:32+08:00
lastmod: 2025-03-29T11:31:32+08:00
draft: false
author: "Steven"
authorLink: "https://github.com/gusanzheng"
tags: ["obsidian"]
categories: ["tutorial"]
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