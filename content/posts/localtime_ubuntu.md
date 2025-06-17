---
title: "Localtime_ubuntu"
subtitle: ""
date: 2025-06-17T20:20:40+08:00
lastmod: 2025-06-17T20:20:40+08:00
draft: false
author: "Steven"
authorLink: "https://github.com/gusanzheng"
description: ""

images: []
resources:
- name: ""
  src: ""

tags: ["docker","ubuntu"]
categories: ["tutorial"]

lightgallery: true

toc:
  auto: false
---
**摘要**
构建一个国内时间、国内镜像源的ubutun基础镜像
<!--more-->

大部分 Docker 镜像都是基于 Alpine，Ubuntu，Debian，CentOS 等基础镜像制作而成。

基本上都采用 `UTC` 时间，默认时区为零时区。

而我们主要用的是 `CST` 时间，北京时间，位于东八区。时区代号： `Asia/Shanghai`


## Ubuntu 20.04

```Dockerfile
# 使用官方 Ubuntu 20.04 镜像作为基础
FROM ubuntu:20.04

# 设置时区环境变量
ENV TZ=Asia/Shanghai

# 设置国内镜像源
RUN mv /etc/apt/sources.list /etc/apt/sources.list.bak
COPY ./sources.list /etc/apt/sources.list

# 更新系统并安装 tzdata，同时设置时区
RUN apt update \
    apt upgrade -y && \
    # 非交互式安装 tzdata，避免出现交互式设置界面
    DEBIAN_FRONTEND=noninteractive apt install -y tzdata && \
    # 将时区信息写入 /etc/timezone 文件
    echo $TZ > /etc/timezone && \
    # 创建符号链接，将系统时区指向指定的时区文件
    ln -sf /usr/share/zoneinfo/$TZ /etc/localtime && \
    # 清理 apt 缓存
    apt clean && \
    # 删除 apt 列表文件，减少镜像大小
    rm -rf /var/lib/apt/lists/*

# 验证时区是否正确设置
RUN date

```

ubuntu 20.04 阿里国内源 `sources.list`
```text
deb http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse 
deb http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse 
deb http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse 
deb http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse 
deb http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse 
deb-src http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse 
deb-src http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse 
deb-src http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse 
deb-src http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse 
deb-src http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
```