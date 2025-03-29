---
title: "Vim Simple Usage"
subtitle: "Vim 简单用法"
date: 2025-03-29T11:45:05+08:00
lastmod: 2025-03-29T11:45:05+08:00
draft: false
author: "Steven"
authorLink: "https://github.com/gusanzheng"
description: ""
license: ""
images: []

tags: ["vim"]
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
使用`vim`的小技巧。

**关键字**
`vim`

<!--more-->

## 1 基础命令
|    命令     |                                 解释                                 |
| :---------: | :------------------------------------------------------------------: |
|     `i`     | 进入输入模式，在光标位置输入文本，按 `ESC`退出输入模式，返回普通模式 |
|     `:`     |                           进入底线命令模式                           |
|     `u`     |                             撤销上次操作                             |
|    `:q`     |                              退出文件,                               |
|    `q!`     |                           不保存，强制退出                           |
|    `:w`     |                               保存文件                               |
|    `:wq`    |                              保存并退出                              |
|  `:set nu`  |                               显示行号                               |
| `:set nonu` |                               关闭行号                               |

## 2 复制删除粘贴跳转
|  命令  |        解释        |
| :----: | :----------------: |
|  `yy`  |   复制光标所在行   |
| `20yy` |    向下复制20行    |
|  `x`   |  删除后面1个字符   |
| `20x`  |  向后删除20个字符  |
|  `dd`  |     删除当前行     |
| `20dd` | 从当前向下剪切20行 |
|  `p`   |        粘贴        |
|  `gg`  |   跳到第1行行首    |
| `20gg` |  跳到第20行的行首  |
|  `G`   |    跳到末行行首    |

## 3 搜索替换
|   命令    |          解释          |
| :-------: | :--------------------: |
|  `/word`  |       搜索 word        |
|    `n`    | 向后重复前一个搜索动作 |
|    `N`    | 向前重复前一个搜索动作 |
| `/\cword` |  进行大小写不敏的搜索  |

- `:%s/word1/word2/g` 全文搜索word1，替换为word2
- `:100,200s/word1/word2/g` 在100~200行搜索word1，替换为word2
- `:100,$s/word1/word2/g` 100行到最后一行搜索word1，替换为word2

## 4 VIM中文乱码的问题

- 修改 `/etc/vim/vimrc` 后，配置会在下次启动 Vim 时自动生效。

```text
" tab, 换行
set expandtab 
set tabstop=4
set softtabstop=4
set shiftwidth=4

" 编码相关
set fileencodings=utf-8,gb2312,gb18030,gbk,ucs-bom,cp936,latin1
set encoding=utf-8
set fileencodings=utf-8,gbk,gb2312,gb18030

" 代码高亮
syntax on
```