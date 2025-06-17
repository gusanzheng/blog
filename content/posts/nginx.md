---
title: "Nginx"
subtitle: ""
date: 2025-06-17T20:04:59+08:00
lastmod: 2025-06-17T20:04:59+08:00
draft: false
author: "Steven"
authorLink: "https://github.com/gusanzheng"
description: ""

images: []
resources:
- name: ""
  src: ""

tags: ["nginx"]
categories: ["tutorial"]

lightgallery: true

toc:
  auto: false
---
**摘要**
nginx 快速实践

<!--more-->

## 1 安装

```bash
apt update && apt upgrade -y
apt install nginx -y
nginx -v
```

## 2 使用

### 2.1 默认端口 80

默认监听 80 端口，可通过 `vim /etc/nginx/sites-available/default` 进行修改

![nginx](/blog/images/nginx.png)

### 2.2 配置

配置文件 `vim /etc/nginx/nginx.conf`

```bash
# 适用于 systemd 系统
# 查看状态
sudo systemctl status nginx
# 启动
sudo systemctl start nginx
# 停止
sudo systemctl stop nginx
# 重启
sudo systemctl restart nginx
# 开启自启动
sudo systemctl enable nginx
# 取消开机自启动
sudo systemctl disable nginx

# 检查配置文件是否合法
nginx -t
# 启动
nginx
# 停止
nginx -s stop
# reload 命令会重新加载配置文件，而nginx服务不会中断
nginx -s reload

# 前台运行，不会退出
nginx -g "daemon off;"
```

> `nginx -g "daemon off;"` 是一个 Nginx 命令，用于确保 Nginx 在前台运行，而不是作为后台进程。<br/ >
> 这是在 Docker 容器中运行 Nginx 时的常见做法，因为 Docker 容器的主进程（PID 1）必须是前台进程，否则容器会立即退出。

```nginx
server {
    listen       83;
    server_name  172.xx.xx.xx;
    charset utf-8; # 防止中文显示出现乱码

    # 配置证书
    # ssl_certificate 证书密钥地址
    # ssl_certificate_key 证书公钥地址
    # ssl_verify_client off;
    # proxy_ssl_verify off;

    location / {
        root /myproject/.vuepress/dist;
        index  index.html;
        try_files $uri $uri/ /index.html;
    }

    error_page 404 /404.html;
    location = /404.html {
        root /myproject/.vuepress/dist;
    }

    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
        root /myproject/.vuepress/dist;
    }
}
```