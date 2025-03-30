---
title: "WSL"
subtitle: ""
date: 2025-03-29T11:53:11+08:00
lastmod: 2025-03-29T11:53:11+08:00
draft: false
author: "Steven"
authorLink: "https://github.com/gusanzheng"
description: ""
license: ""
images: []

tags: ["wsl"]
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
简单使用`wsl`。

**关键词**
`wsl`

<!--more-->

## 1 启用 wsl2.0

参考 [WSL2 配置过程及常用命令 - 博客](https://www.cnblogs.com/o2iginal/p/17755371.html)

```bash
wsl --status
wsl -l -v 查看状态
wsl --shutdown 关闭wsl
```

安装好后，重新打开终端，应该就可以看到安装的ubuntu选项了

## 2 wsl迁移至D盘

参考 [如何将WSL从C盘迁移到其他盘区 - 博客](https://zhuanlan.zhihu.com/p/621873601)

```bash
# 导出备份
wsl --export Ubuntu-22.04 D:\Ubuntu_WSL\Ubuntu.tar
# 确定在此目录下可以看见备份Ubuntu.tar文件之后，注销原有的wsl
wsl --unregister Ubuntu-22.04
# 将备份文件恢复到D:\Ubuntu_WSL中去
wsl --import Ubuntu-22.04 D:\Ubuntu_WSL D:\Ubuntu_WSL\Ubuntu.tar
```

## 3 更改默认登录用户为 steven

在 wsl 内，`vim /etc/wsl.conf`

```toml
[user]
default=steven
```

随后退出 wsl，关闭wsl。

```bash
exit
wsl --shutdown
```

重新打开wsl，就会以用户 elvin 登录wsl了。