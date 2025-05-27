---
title: "Linux_cmd"
subtitle: "Linux 常用命令"
date: 2025-05-27T13:36:08+08:00
lastmod: 2025-05-27T13:36:08+08:00
draft: false
author: "Steven"
authorLink: "https://github.com/gusanzheng"
description: ""
license: ""
images: []

tags: ["linux"]
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

**关键词**

<!--more-->

## 时区配置
```bash
把/usr/share/zoneinfo/Asia/Shanghai
放到/etc/localtime 即可
```

## 终端配色
```bash
vim ~/.bashrc
source ~/.bashrc

# --color=auto 即可让文件变彩色
alias l='ls --color=auto'
alias ls='ls --color=auto'
alias ll='ls -l --color=auto'

# 配色方案
PS1="\[\e[37;1m[\] \[\e[32;1m\]\u \[\e[39;1m\]@ \[\e[33;1m\]\h \[\e[36;1m\]\w \[\e[37;1m\]] \t \n\[\e[32;1m\]\$ \[\e[0m\]"

# 这样可以显示本机Ip
IP_ADDR=$(hostname -i)
PS1="\[\e[37;1m[\] \[\e[32;1m\]\u \[\e[39;1m\]@ \[\e[33;1m\]$IP_ADDR \[\e[36;1m\]\w \[\e[37;1m\]] \t \n\[\e[32;1m\]\$ \[\e[0m\]"
```

## 用户管理

```bash
adduser steven # 添加用户
passwd steven # 修改用户的密码
cat /etc/passwd # 查看所有用户 
userdel -r haha # 完全删除用户所有东西

# 给用户添加sudo权限
vim /etc/sudoers
# 找到root ALL = (ALL) ALL这一行，在下一行加入username ALL = (ALL) ALL。
su steven
sudo whoami #如果返回root，则权限添加成功

# 添加到 docker 组，以获得访问 Docker 守护进程的权限。
sudo usermod -aG docker steven

chsh -s /bin/bash steven # 为用户切换默认shell为bash
```
## 组管理

```bash
cat /etc/group # 查看所有组
groups steven # 查看steven所在的所有组
getent group sudo # 查看sudo组里有哪些用户
usermod -aG docker steven # 添加用户steven到docker组
gpasswd -d steven docker # 从docker组里移除steven用户
```

## time 计时操作

```bash
time ./test 计时test操作的耗时
timeout 5m ./test 执行5min，test 程序
```

## at 定时执行

```bash
echo "kill -9 101618" | at now + 3 hours
at now + 30 minutes
```

## chmod 文件权限管理

```bash
chmod u+x <filename> # 给文件增加可执行权限
chown -R steven /home/dev # 将 /home/dev 目录owner给steven
chgrp -R steven /home/dev # 将 /home/dev 目录组改为steven
```


## ln 软/硬链接
```bash
ln -s /usr/bin/src /usr/bin/target  # 将src软连接到target，注意要使用绝对地址
# 默认是硬连接，-s 表示软连接

# 删除链接
rm <软连接文件名> # 只会删除软连接，不会影响源文件
rm  <硬链接文件名> # 会删除文件计数，当所有硬链接都没了，那么源文件也就没了
```

## tar 压缩，解压缩
```bash
# 压缩
tar -zcvf test.tar.gz <filename>
# 解压缩
tar -zxvf test.tar.gz -C <path>
```


## grep 过滤，统计
```bash

grep test.txt 'hello'

# flag 选项
-r 递归
-i 不区分大小写
-v 'xx' 排除包含xx的行
-E 使用正则表达式

# 快速统计该文件夹下文件的个数
ls -l | grep "^-" | wc -l
# 统计文件夹的个数
ls -l | grep "^d" | wc -l
```

## xargs  将标准输入（stdin）的数据转换为命令行参数

```bash
echo "file1.txt file2.txt file3.txt" | xargs -I {} echo "Processing {}"
# 批量删除文件 
echo "file1.txt file2.txt file3.txt" | xargs rm
# 找到并删除
find . -name "*.log" | xargs rm -f

# flag 选项
-I {}：指定占位符，用于替换每个输入项
```


## tmux 分屏

```bash
tmux ls 显示所有会话
tmux new -s dev 创建一个新的会话名为dev
tmux attach -t <会话序列号>  接入会话
tmux detach 退出会话
tmux kill-session -t 会话名 关闭

ctrl b 开启命令模式
:set -g mouse on # 支持鼠标，可以滚轮
d 退出会话

窗格 panel 操作
% 左右平分出两个窗格
# " 上下平分出两个窗格
x 关闭当前窗格
左右键切换窗格

（很少用）窗口 window操作
c 新建窗口，此时当前窗口会切换至新窗口，不影响原有窗口的状态
# p 切换至上一窗口
n 切换至下一窗口
w 窗口列表选择
& 关闭当前窗口
```