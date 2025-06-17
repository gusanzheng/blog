---
title: "Ffmpeg"
subtitle: ""
date: 2025-06-17T20:09:22+08:00
lastmod: 2025-06-17T20:09:22+08:00
draft: false
author: "Steven"
authorLink: "https://github.com/gusanzheng"
description: ""

images: []
resources:
- name: ""
  src: ""

tags: ["ffmpeg"]
categories: ["tutorial"]

lightgallery: true

toc:
  auto: false
---
**摘要**
ffmpeg 快速实践

<!--more-->

## 1 安装ffmpeg

ubuntu，**使用官方源**，可进行在线安装
```bash
apt update
apt upgrade -y
apt install ffmpeg

ffmpeg -version
```
## 2 命令参数

```bash
-ar 采样率

-ac 通道

-sample_fmt 位深 s16

-acodec 编解码器 libmp3lame

-ab 指定比特率，对应格式 CBR
  -ab 8k, 16k, 24k, 32k, 40k, 48k, 64k, 80k, 96k, 112k, 128k, 160k, 192k, 224k, 256k, or 320k
-compression_level 0：禁用压缩（将其设置为0确保编码为CBR）。
-write_xing 0：禁用Xing header（默认情况下，LAME会写入VBR头，但我们不需要它）

-aq 音频质量，对应格式 VBR
  -aq 1 ~ 9， 数字越小，音频质量越高
```

## 3 实践

```bash
# pcm ---> mp3
ffmpeg -f s16le -ar 24000 -ac 1 -sample_fmt s16p -i ori_24k.pcm -acodec libmp3lame -b:a 32k -compression_level 0 -write_xing 0 ffmpeg32_24k.mp3

# mp3 ---> pcm
ffmpeg  -i ori_16k.mp3 -f s16le -ar 16000 -ac 1 -sample_fmt s16 -acodec pcm_s16le out_16k.pcm
```

