---
title: "Gdb Gcc CMake"
subtitle: ""
date: 2025-03-30T15:54:30+08:00
lastmod: 2025-03-30T15:54:30+08:00
draft: false
author: "Steven"
authorLink: "https://github.com/gusanzheng"
description: ""
license: ""
images: []

tags: ["gdb","coredump",gcc","cmake"]
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
简单使用gdb gcc cmake。

**关键词**
`gdb`, `coredump`, `gcc`, `cmake`

<!--more-->

## 1. gdb
- 权限问题：如果目标进程需要 root 权限，确保以 root 用户运行 GDB。
- 调试符号：如果可执行文件没有调试符号（未使用 -g 编译），堆栈信息可能不完整。

### 启动
```bash
# -g 编译，表示可以debug
g++ -g test.cpp -o test 

# gdb 调试二进制文件
gdb test
或 gdb -q test //表示不打印gdb版本信息，比较干净
break（b）main.main #在main包里的main函数入口打断点
continue（c） #继续运行，直到断点处停止

# 用gdb -args 给二进制文件添加启动参数，并监控
gdb --args ./test -c 100

# 监听进程
gdb attach <pid> 
或者 gdb -p <pid> 
hanle SIGPIPE nostop
c

# 调试 core 文件
gdb ./可执行文件 core
```
### 常用命令

```bash
run # 开始运行
step # 单步执行，会进入函数
next # 单步执行，不进入函数
continue # 继续执行
finish # 完成执行，从当前函数返回
quit # 退出gdb

break 文件名:行号 # 设置断点
info breakpoints # 查看断点信息
delete <断点编号> # 删除断点，如果不指定断点编号会删除所有断点
disable <断点编号> # 停用断点
enable <断点编号> # 启用断点

list # 显示代码
list <函数名> # 显示指定函数的代码
print <变量名> # 打印变量值
ptype <变量名> # 打印变量类型
x/<格式> <地址> # 查看内存

info registers # 查看寄存器信息
info threads # 查看所有线程的信息
info frame # 查看当前栈帧详细信息
info locals # 显示当前栈帧中的所有局部变量及其值
info args  # 显示当前栈帧中的函数参数及其值
info signals # 列出所有信号及其处理方式

backtrace # bt 打印崩溃堆栈
```

### 打印所有线程的堆栈信息，保存到文件
```bash
# 多线程程序
set logging enabled on
thread apply all bt # 打印所有线程的堆栈信息
set logging file gdb_log.txt
set logging enabled of
```

## 2. core dump
- 查看是否支持core dump
- 通过 /proc/sys/kernel/core_pattern 控制 core 文件的生成路径和命名规则
```bash
ulimit -a

ulimit -c
# 如果返回 0，则不支持，可通过 ulimit -c unlimited 来修改
# 如果返回 unlimited，则支持，且大小无限
```

## 3. gcc/g++

```bash
# （1）预处理（Preprocessing）。由预处理器cpp完成，将.cpp源文件预处理为.i文件。
g++  -E  test.cpp  -o  test.i    # 生成预处理后的.i文件
# （2）编译（Compilation）。由编译器cc1plus完成，将.i文件编译为.s的汇编文件。
# 使用-S选项，只进行编译而不进行汇编，生成汇编代码。
g++ -S test.i -o test.s                        # 生成汇编.s文件
# （3）汇编（Assembly）。由汇编器as完成，将.s文件汇编成.o的二进制目标文件
g++  -c  test.s  -o  test.o    # 生成二进制.o文件
# （4）链接（Linking）。由链接器ld，将.o文件连接生成可执行程序。
g++ test.o  -o  test.out            # 生成二进制.out可执行文件

# 生成可执行文件
g++ -o hello hello.cpp

# 生成动态链接库
g++ -fPIC -shared -o libexample.so example.cpp
```
功能参数
- `-I` 头文件搜索路径
- `-L` 库文件搜索路径
- `-lpthread` 搜索库文件pthread
- `-g` debug模式，gdb会有更详细信息
- `-std=c++11` 告诉编译器使用 C++11 标准。

## 4. CMake

参考：[CMake 快速入坑 - 博客](https://dsyx.github.io/2020/12/30/cmake-quick-start/)

编写 `CMakeLists.txt`
```text
cmake_minimum_required(VERSION 3.0.0)
project(hello)
add_executable(hello main.cpp)
```

执行
```bash
mkdir build && cd build
cmake ../ # 生成 makefile
cmake --build . # 构建可执行文件
或者直接 make .
```