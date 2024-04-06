---
title: "在 Windows 环境下配置 CGO"
meta_title: ""
description: "本文讲述了如何在 Windows 环境下配置 CGO 进行编译。"
categories: ["golang"]
author: "Akaishi Toshiya"
date: 2024-04-06T16:10:00+08:00
tags: ["golang", "cgo"]
draft: false
---

如果你在编译 go 项目的时候，出现了下面的提示，那么看这篇文章就对了。

```bash
exec: "gcc": executable file not found in %PATH%
```

这通常是因为，你的 go 环境没有为 CGO 配置好 gcc 编译器。

## CGO 是什么

> CGO 是 GO 语言里面的一个特性，CGO 属于 GOLANG 的高级用法，主要是通过使用 GOLANG 调用 CLANG 实现的程序库

## 安装 GCC

在 Windows 下安装 gcc，本文讲述的是通过安装 MinGW-w64 来实现，如果这个不是你想要的结果请参考其他文章。

[MinGW Builds - Github Releases](https://github.com/niXman/mingw-builds-binaries/releases)

可以从上面的地址下载 MinGW-w64 的发行包。

可以看到 win32 有两种发行包可以选择： msvcrt 和 ucrt，从名字上来看，猜测应该 msvcrt 是使用 Microsoft Visual C++ 的环境来编译的。
ucrt 不明，暂时我们先选择 msvcrt 版本吧。

由于下载的格式是 7z，如果你不能打开此文件，可能还需要下载 7-zip。

解压到一个位置，比如 `D:\MinGW`。

## 添加环境变量

然后在系统的 `path` 变量中，加入 `D:\MinGW\bin`。

再重试编译使用 CGO 的库的程序时，应该就不会提示找不到 gcc 了。
