---
title: "Git"
subtitle: ""
date: 2025-03-30T16:01:52+08:00
lastmod: 2025-03-30T16:01:52+08:00
draft: false
author: "Steven"
authorLink: "https://github.com/gusanzheng"
tags: ["git"]
categories: ["tutorial"]
---
**摘要**
简单使用`git`。

**关键词**
`git`, `git lfs`

<!--more-->

- Git：适合管理文本文件和小型二进制文件，但对于大文件（如图片、视频、数据集等），Git 的效率较低，因为每次更改都会存储整个文件的副本。
- Git LFS：通过将大文件存储在远程服务器上，并在 Git 仓库中仅保存指向这些文件的指针，从而减少仓库的大小和历史记录的负担。

## 1. Git 基础

### 1.1 基础设置

```bash
# 查看配置
git config --list

# 设置用户名和邮箱
git config --global user.name <user_name>
git config --global user.email <user_email>

# 取消配置
git config --global --unset xx

# 初始化仓库，并设置初始化分支为master
git init --initial-branch=master。

# 添加远程仓库地址
git remote add origin <link>

# 显示当前远程仓库链接
git remote -v

# 更改远程仓库地址，比如更改为 git@xxx
git remote set-url origin <link>
```

### 1.2 基础操作

```bash
# 克隆仓库
git clone <link>

# 将工作空间的代码添加到缓冲区
git add .

# 将缓冲区的代码推送到本地仓库
git commit -am "init commit"
# 这时，如果想修改commit信息，可以
git commit --amend -m "新的提交信息"

# 将本底仓库的master分支推送到远程仓库的master分治
git push origin master:master

# 查看当前状态
git status

# 查看历史
git log
# --oneline：列表格式。
# --graph：以 ASCII 图形显示分支与合并历史。
# --stat：在每个提交后面显示修改的文件和修改的行的统计信息

# 当前分支对比 origin/dev 分支，只对比 src 文件夹，并排除 src/vendor, src/codec 文件夹
git diff origin/dev -- src ":(exclude)src/vendor" ":(exclude)src/codec"
```


### 1.3 git branch 分支操作

```bash
# 查看本地分支
git branch

# 获取远程仓库的信息
git fetch origin
# 查看远程分支
git branch -r

# 删除分支
git branch -d 分支名

# 删除远程仓库的分支
git push origin :分支名

# 切换分支
git checkout 分支名

# 创建并切换到新分支
git checkout -b 新分支名

# 切换到远程的 dev 分支
git checkout -b dev origin/dev

# 将远程 dev 分支的最新代码拉取到本地 dev 分支
git pull origin dev:dev
# 相当于
git fetch origin dev
git merge origin/dev

# 将远程 dev 分支直接覆盖本地 dev 分支
git reset --hard origin/dev

# 将当前分支重命名为main
git branch -m main

# --orphan 的作用一个是拷贝当前所在分支的所有文件
# 另一个是使新分支没有父结点，即没有历史记录，是一个完全独立背景干净的分支
git checkout --orphan new-branch
```
### 1.4 git tag 标签操作

```bash
# 创建标签并附带注释
git tag -s v1.0 -m "Release version 1.0"

# 获取远程分支信息
git fetch origin --tags

# 查看远程仓库的标签
git ls-remote --tags origin

# 切换到远程的 v1.0.0 tag
git checkout -b v1.0.0 tags/v1.0.0
```

## 2. Git 进阶

### 2.1 Git restore 回滚文件

```bash
# 撤销对工作目录中的修改，不影响暂存区
git restore test.txt
```

### 2.2 Git reset 回滚操作

```bash
# 保留工作区和缓存区，仅撤销 git commit， 文件的状态从“已提交”变为“已暂存”
git reset --soft HEAD~1

# (默认 --mixed)保留工作区，撤销 git add 和 git commit, 文件的状态从“已提交”变为“未暂存”
git reset HEAD~1

# 全部恢复到上一版本，什么都不保存
git reset --hard HEAD~1

HEAD：表示当前分支的最新提交。
HEAD~1：表示当前分支的倒数第2次提交。
HEAD~2：表示当前分支的倒数第3次提交。
HEAD~3：表示当前分支的倒数第4次提交。
```
### 2.3 Git stash 临时保存修改，以便切换分支

```bash
用于临时保存当前工作目录的更改，以便你可以切换到其他分支或进行其他操作，而不会丢失未提交的更改
# 将所有未提交的更改保存到一个栈中，并将工作目录恢复到最近一次提交的状态
git stash

# 查看 stash 列表
git stash list

# 应用最近一次保存的 stash
git stash apply
git stash apply stash@{n} # 应用特定的 stash

# 应用并删除最近一次保存的 stash
git stash pop
git stash pop stash@{n}

# 删除 stash
git stash drop
git stash drop stash@{n}
git stash clear # 清空

# 查看 stash 详细信息
git stash show stash@{0}
```
### 2.4 Git merge 将变更合并进来，生成新的提交

```bash
# 处在 master 分支，把dev分支的内容合并进来
git merge dev

### 遇到合并冲突 ### 
# 标记已处理完冲突
git add .
git commit -am "merged dev"
# 取消合并
git merge --abort
```
### 2.5 Git rebase 变基, 将变更内容重放

```bash
# 交互
git rebase -i [commitid]
git push -f origin master:master

# 处于 dev 分支
git rebase master
1. 找到 master 和 dev 的一个共同祖先。
2. 将 dev 相对于共同祖先的修改在 master 上进行重演。
3. 解决冲突：git add . 
4. 然后 git rebase --continue。

# 取消
git rebase --abort

# 将本地的修改在新的 master 上进行重演
git pull --rebase origin master
# 相当于
git fetch origin master
git rebase master
```
### 2.6 Git cherry-pick 将某个特定的提交（commit）从一个分支应用到当前分支上

```bash
git cherry-pick <commit-hash>
```

## 3. Git lfs 大文件操作

```bash
apt install git-lfs

git lfs track "*.mp4" # 跟踪
git lfs track # 查看当前跟踪的大文件

git add .gitattributes # 跟踪规则会保存在 .gitattributes 文件中
git commit -m "Track *.mp4 files with Git LFS"

# 后续可以像普通文件一样提交大文件了

git clone # 不会拉取大文件
git lfs ls-files # 查看跟踪了哪些大文件
git lfs install # 初始化 Git LFS
git lfs pull # 拉取大文件
git lfs pull --include="path/to/file" # 拉取指定大文件
```

## 4. Git submodule 子模块操作

### 4.1 添加子模块

```bash
# 添加子模块
git submodule add <仓库URL> <路径>
git submodule add https://github.com/example/library.git libs/library

# 添加指定分支的子模块
git submodule add -b <分支名> <仓库URL> <路径>
```

### 4.2 初始化和更新子模块

```bash
# 初始化子模块配置
git submodule init

# 更新子模块代码
git submodule update

# 初始化并更新（一次性）
git submodule update --init

# 递归初始化和更新所有嵌套子模块
git submodule update --init --recursive

# 克隆包含子模块的仓库时使用
git clone --recursive <仓库URL>
```

### 4.3 查看子模块状态

```bash
# 查看子模块状态
git submodule status

# 查看详细状态信息
git submodule status --recursive

# 使用 git status 查看
git status
```

### 4.4 更新子模块

```bash
# 更新到子模块的最新提交
git submodule update --remote

# 更新到特定分支的最新提交
git submodule update --remote --merge

# 更新特定子模块
git submodule update --remote <子模块路径>
```

### 4.5 修改子模块

```bash
# 进入子模块目录进行修改
cd <子模块路径>
# 进行常规git操作
git add .
git commit -m "修改内容"
git push

# 回到主项目，提交子模块更新
cd ..
git add <子模块路径>
git commit -m "更新子模块"
git push
```

### 4.6 删除子模块

```bash
# 1. 从.gitmodules文件中删除相关条目
git config --file .gitmodules --remove-section submodule.<子模块名>

# 2. 从.git/config中删除相关条目
git config --remove-section submodule.<子模块名>

# 3. 从索引中删除
git rm --cached <子模块路径>

# 4. 删除工作目录中的子模块文件（如果需要）
rm -rf <子模块路径>

# 5. 提交更改
git commit -m "删除子模块"
```