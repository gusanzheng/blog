---
title: "Deploy Hugo Personal Website"
subtitle: "使用Hugo部署个人网站"
date: 2025-03-29T10:43:35+08:00
lastmod: 2025-03-29T10:43:35+08:00
draft: false
author: "Steven"
authorLink: "https://github.com/gusanzheng"
tags: ["hugo", "github page"]
categories: ["tutorial"]
---
**摘要**
使用Hugo，在Github Page上部署个人网站。

**关键词**
`hugo`, `LoveIt`, `github page`, 个人网站

<!--more-->

**相关链接**
- [Hugo 官网](https://gohugo.io/)
- [LoveIt 主题](https://hugoloveit.com/)
- [Host on github - 官方教程](https://gohugo.io/hosting-and-deployment/hosting-on-github/)

---

## 安装Hugo
```bash
go install github.com/gohugoio/hugo@latest
hugo version
```

## 创建个人网站

初始化
```bash
hugo new site my_website
cd my_website
git init # 初始化github仓库
```

下载主题
```bash
git clone https://github.com/dillonzq/LoveIt.git themes/LoveIt
git submodule add https://github.com/dillonzq/LoveIt.git themes/LoveIt
```

修改`hugo.toml`, 可参考`LoveIt`主题教程 [theme-documentation-basics](https://hugoloveit.com/theme-documentation-basics/)

开始写文章
```bash
hugo new posts/first_post.md

hugo build # build web, 文件会放在 public
hugo serve # build and start web server
hugo serve -D # 包含 draft 内容(.md文件最上面有draft=true)
```

## 部署到 Github Page
配置`.github/workflows/hugo.yaml`，可查看[Host on github - 官方教程](https://gohugo.io/hosting-and-deployment/hosting-on-github/)
