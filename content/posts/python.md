---
title: "Python Tools"
subtitle: "Python相关工具链"
date: 2025-03-30T15:45:52+08:00
lastmod: 2025-03-30T15:45:52+08:00
draft: false
author: "Steven"
authorLink: "https://github.com/gusanzheng"
tags: ["python"]
categories: ["python"]
---
**摘要**
简单使用python相关工具链。

**关键词**
`python`, `uv`, `pip`

<!--more-->

## `uv`

参考文档
- [uv - 官方文档](https://docs.astral.sh/uv/reference/cli/)

```bash
uv venv -p 3.12 # 创建虚拟环境
source .venv/bin/activate # 进入虚拟环境
uv sync # 同步环境
deactivate # 退出虚拟环境

uv add requests  
uv tree
uv remove requests
```

## `pip`设置国内镜像源
修改配置
```bash
pip config list

pip config set global.index-url https://mirrors.aliyun.com/pypi/simple
pip config set install.trusted-host mirrors.aliyun.com
```

或者直接修改配置文件`~/.pip/pip.conf`
```text
[global]
index-url = https://mirrors.aliyun.com/pypi/simple

[install]
trusted-host=mirrors.aliyun.com
```

单次使用
```bash
pip install xxxx -i http://mirrors.aliyun.com/pypi/simple/ --trusted-host mirrors.aliyun.com
```