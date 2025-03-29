---
title: "Quickly Understand Golang1.24.0"
subtitle: "快速了解golang1.24.0"
date: 2025-03-28T18:22:43+08:00
lastmod: 2025-03-28T18:22:43+08:00
draft: false
author: "Steven"
authorLink: "https://github.com/gusanzheng"
description: ""
license: ""
images: []

tags: ["golang"]
categories: ["golang"]

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
2025/02/11, golang更新至1.24.0版本。

**关键字**
`go1.24.0`, `Swiss Table`, `weak pointer`, `omitzero`, `generic type aliases`

<!--more-->

**相关链接**
- [Go 1.24 Release Notes - 官网](https://tip.golang.org/doc/go1.24?utm_source=chatgpt.com)
- [Go 1.24 interactive tour - 博客](https://antonz.org/go-1-24/)

---

## 1 性能提升

### 1.1 `map`底层更新为`Swiss Table`

性能提升
- 大型（>1024 个条目）映射的访问和分配提高了 ~30%。
- 分配到预分配大小的地图提高了 ~35%。
- 整体迭代速度加快 ~10%，对于低负载（大尺寸、少条目）的map，则提高 ~60%。

### 1.2 `sync.Map` 底层更新为`HashTrieMap`

`sync.Map` 被重新实现为基于**并发哈希Trie（HashTrieMap）**，替代原有结构。这一数据结构最初在 Go 1.23 的 `unique` 包中验证成功

**性能提升**
- 写操作优化：`LoadOrStore`、`Swap`、`Delete` 等写密集型操作性能提升 **80%+**（如 `MapLoadOrStoreUnique` 提升80.88%）
- 范围遍历：`MapRange` 性能提升37.23%
- 内存管理：及时收缩机制（如 `MapAdversarialDelete` 性能提升94.3%）
- 并发冲突减少：对不重叠的键集操作时竞争显著降低

**特性优势**
- **无预热需求**：旧版需要积累足够操作才会优化，新版直接低竞争
- **细粒度锁**：通过哈希Trie的层级结构实现更细粒度的并发控制
- **及时回收**：删除元素后立即释放内存，旧版依赖分代（dirty map）机制

**性能取舍**
- 读多场景轻微下降：如 `MapLoadMostlyHits` 增加6.93%（旧版瑞士表优化更适配纯读）
- 部分删除操作波动：如 `MapCompareAndDeleteMostlyHits` 性能下降117%（可能因收缩机制触发额外开销）

**场景**
- 频繁键更新、大规模键值对删除、并发范围遍历的操作场景将显著受益
- 纯读密集型场景建议结合基准测试评估影响。

## 2 新特性

### 2.1 JSON 支持 `omitzero`

```go
package main

import (
	"encoding/json"
	"fmt"
	"time"
)

type Person1 struct {
	Name       string    `json:"name"`
	Hobby1     string    `json:"hobby,omitempty"`
	BirthDate1 time.Time `json:"birth_date,omitempty"`
}
type Person2 struct {
	Name      string    `json:"name"`
	Hobby     string    `json:"hobby,omitzero"`
	BirthDate time.Time `json:"birth_date,omitzero"`
}

func main() {
	alice := Person1{Name: "Alice"}
	b1, _ := json.Marshal(alice)
	fmt.Println(string(b1)) // {"name":"Alice","birth_date":"0001-01-01T00:00:00Z"}

	danny := Person2{Name: "Danny"}
	b2, _ := json.Marshal(danny)
	fmt.Println(string(b2)) // {"name":"Danny"}
}

```

### 2.2 支持泛型类型别名

```go
// 在 go1.24.0 之前，这个是非法的
type Set[T comparable] = map[T]bool
set := Set[string]{"one": true, "two": true}

fmt.Println("'one' in set:", set["one"])
fmt.Println("'six' in set:", set["six"])
```

### 2.3 **零分配追加模式**  

新增 `encoding.TextAppender` 和 `encoding.BinaryAppender` 接口，允许将对象序列化到现有字节切片末尾，**避免重复内存分配**。

```go
type TextAppender interface {
    AppendText(b []byte) ([]byte, error)  // 追加文本表示
}
type BinaryAppender interface {
    AppendBinary(b []byte) ([]byte, error) // 追加二进制表示
}

// 复用预分配缓冲区
buf := make([]byte, 0, 1024)
for _, obj := range objects {
    buf, _ = obj.AppendText(buf)
}
```

**注意**
- 禁止修改输入切片 `b[:len(b)]` 的现有内容
- 禁止保留输入切片的引用（防止意外篡改）

**已实现类型示例：**
- `time.Time`：时间格式化（RFC3339）
- `net.IP`：IP 地址字符串/二进制表示
- `math/big.Float`：高精度浮点文本编码
- `regexp.Regexp`：正则表达式模式串

```go
t := time.Now()

var b []byte
b, err := t.AppendText(b)
fmt.Printf("b=%s, err=%v\n", b, err)
```
## 3 标准库

### 3.1 新增`os.Root`支持指定目录范围的文件系统访问 

```text
.
├── 2.txt
├── data
│   └── 1.txt
├── go.mod
└── main.go
```

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	dir, err := os.OpenRoot("data")
	if err != nil {
		panic(err)
	}
	fmt.Printf("opened root=%s, err=%v\n", dir.Name(), err)
	defer dir.Close()

	file, err := dir.Open("1.txt")
	if err != nil {
		panic(err)
	}
	fmt.Printf("opened file=%s, err=%v\n", file.Name(), err)

	file, err = dir.Open("../2.txt")
	if err != nil {
		panic(err)
	}
	fmt.Printf("opened file=%v, err=%v\n", file, err)
}


```

output
```text
opened root=data, err=<nil>
opened file=data/1.txt, err=<nil>
panic: openat ../2.txt: path escapes from parent

goroutine 1 [running]:
main.main()
        /home/elvin/project/main.go:24 +0x272
exit status 2
```

### 3.2 新增弱指针 `weak pointer`

弱指针通常用于避免循环引用，特别是在涉及垃圾回收的场景中。

弱指针不会增加对象的引用计数，因此不会阻止对象被垃圾回收。

运行时现在提供了一个新的终结机制 `runtime.AddCleanup`，它比 `runtime.SetFinalizer` 更加灵活、高效，并且更不容易出错。可以指定回收目标时可调用的 cleanup function。

## 4 `testing` 包改进

### 4.1 基准测试优化：`testing.B.Loop`

替代传统的 `for range b.N` 循环，通过 `b.Loop()` 实现更简洁的基准测试结构。注意两者不可混用

```go
func BenchmarkX(b *testing.B) {
    setup()          // 初始化（仅执行一次）
    for b.Loop() {    // 自动处理循环次数
        targetCode()  // 被测代码
    }
}
```

- **单次初始化**：旧版每次 `-count` 重复执行时，`setup()` 代码会多次运行；新版 `setup()` 仅执行一次
- **自动计时**：无需手动 `b.ResetTimer()`，循环外的代码自动不计入耗时
- **防编译器优化**：保证循环体内的代码不会被优化消除（如 `sink` 变量不再需要）

### 4.2 其他更新

- 时间沙盒 `testing/synctest`
- 自动取消的上下文 `T.Context/B.Context`
- 目录隔离 `T.Chdir/B.Chdir`

## 5 其他更新

- Go 1.24 requires Linux kernel version 3.2 or later.
- `string`和`bytes`迭代器（iterators），使得处理字符串和字节数据更加方便和高效。 
- 禁止无意义的 `panic(nil)` 调用，避免掩盖真实错误
- `go install` 命令必须包含 `@latest`
- `go build`、`go install` 和 `go test` 命令现在接受 `-json` 标志
- `go` 命令现在提供了一个机制来跟踪模块的工具依赖。
- `Go 1.24` 新增了 `go:wasmexport` 指令，用于将 `Go` 程序的函数导出到 `WebAssembly` 主机。同时，`Go` 现在支持将程序构建为 `WASI reactor/library`。
- `crypto` 有很多更新
...