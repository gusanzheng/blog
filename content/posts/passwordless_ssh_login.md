---
title: "Password-less SSH Login"
subtitle: "SSH免密登录"
date: 2025-03-29T12:05:56+08:00
lastmod: 2025-03-29T12:05:56+08:00
draft: false
author: "Steven"
authorLink: "https://github.com/gusanzheng"

tags: ["ssh"]
categories: ["tutorial"]
---

**摘要**
实现`ssh`免密登录。

**关键词**
`ssh`

<!--more-->

## 1 生成公钥

```bash
# 生成公钥
ssh-keygen -t rsa -C "your_email_address"
```

## 2 免密登录 Git

将 `C:\Users\steven\.ssh\gitee_id_rsa.pub` 的内容复制到gitee, github的`SSH keys`

测试连通性
```bash
ssh -T git@gitee.com
```

后续使用`git clone git@gitee.com:steven/my-project.git` 即可。

有时候会需要 `C:\Users\steven\.ssh\config` 正确配置Host信息
```text
Host gitee.com
  HostName gitee.com
  IdentityFile C:\Users\steven\.ssh\gitee_id_rsa
  User git

Host github.com
  HostName github.com
  IdentityFile C:\Users\steven\.ssh\github_id_rsa
  User git
  ProxyCommand nc -X connect -x 127.0.0.1:7890 %h %p
```
> ProxyCommand 配置 ssh 的代理，加速 git clone 拉取 git@github.com 仓库的速度

如果远程仓库一开始用的是HTTPS，可以改为ssh
```bash
# 查看远程仓库地址
git remote -v
# 更换远程仓库地址
git remote set-url origin git@gitee.com:steven/my-project.git
```

## 3. 免密登录服务器
### 3.1 ssh终端工具
使用ssh终端工具，比如xshell, windterm时，第一次填写密码后，会记住密码，后续无需手动输入。

配置跳板机、代理可参考
- [Xshell通过跳板机连接内网服务器_xshell使用跳板机连接内网服务器-CSDN博客](https://blog.csdn.net/qq_39387856/article/details/120569650)
- [Xshell+Xftp通过代理的方式访问局域网内网服务器_xshell代理原理-CSDN博客](https://blog.csdn.net/guxiaohai_/article/details/134393391)

### 3.2 vscode remote-ssh
使用vscode remote-ssh时，可以将本机生成的公钥`id_rsa.pub`的内容放到目标服务器的`~/.ssh/authorized_keys`中。

需要本机 `C:\Users\steven\.ssh\config` 正确配置Host信息
```text
Host dev-xxx.xxx.xxx.xxx
    HostName xxx.xxx.xxx.xxx
    User steven

Host production-xxx.xxx.xxx.xxx
    HostName xxx.xxx.xxx.xxx
    User steven
    ProxyJump jump_machine

Host jump_machine
    HostName xxx.xxx.xxx.xxx
    User steven
```

如果没有实现免密扽登录，可在目标服务器尝试执行如下命令
```bash
chmod 700 ./.ssh
chmod 600 ./.ssh/authorized_keys
```
原因是SSH不希望用户目录和~/.ssh目录对组有写权限