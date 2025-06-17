---
title: "Vuepress"
subtitle: ""
date: 2025-06-17T20:13:02+08:00
lastmod: 2025-06-17T20:13:02+08:00
draft: false
author: "Steven"
authorLink: "https://github.com/gusanzheng"
description: ""

images: []
resources:
- name: ""
  src: ""

tags: ["vuepress"]
categories: ["tutorial"]

lightgallery: true

toc:
  auto: false
---
**摘要**
vuepress 快速实践

<!--more-->

## 1 参考文档

- [vuepress快速上手](https://vuepress.vuejs.org/zh/guide/getting-started.html)

## 2 开发

目录结构

```bash
docs
|-- .vuepress
|-- FAQ
|-- tts
```


`package.json`

```json
{
  "scripts": {
    "docs:build": "vuepress build src",
    "docs:clean-dev": "vuepress dev src --clean-cache",
    "docs:dev": "vuepress dev src"
  }
}
```

`docs/.vuepress/config.js`

```js
import { viteBundler } from '@vuepress/bundler-vite'
import { defaultTheme } from '@vuepress/theme-default'
import { defineUserConfig } from 'vuepress'

export default defineUserConfig({
    lang: 'zh-CN',
    title: 'ttsdoc', // 设置网站标题
    description: '语音合成服务文档库',
    head: [
        // 站点图标
        ["link", { rel: "icon", href: "/public/favicon.ico" }],
    ],
    bundler: viteBundler(),
    theme: defaultTheme({
        logo: "/logo.png",
        sidebar: [
            {
                text: '首页',
                prefix: '/FAQ/',
                link: '/',
                children: [
                    { text: '常见问题', link: 'faq.md' },
                ]
            },
            {
                text: '通用合成',
                prefix: '/tts/',
                link: '/tts/profile.md',
                children: [
                    { text: '接口文档', link: 'tts_readme.md' },
                ]
            },
        ],
    })
})
```

```bash
npm run docs:build
```
