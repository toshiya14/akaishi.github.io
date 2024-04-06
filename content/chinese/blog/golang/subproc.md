---
title: "Golang 启动子进程并处理 stdout 消息"
meta_title: ""
description: "本文讲述了如何在 golang 中启动子进程并处理 stdout 消息。"
categories: ["golang"]
author: "Akaishi Toshiya"
date: 2024-04-06T16:04:00+08:00
tags: ["golang"]
draft: false
---

## 伸手党福利

你可以从下面的位置获取源代码。

[subproc - Gists - Akaishi Toshiya](https://gist.github.com/toshiya14/695dfd6520abf3bf4b1c78b3746762da)

三个文件均需要导入到你的项目中。

## 如何使用

例如需要处理 `a.exe -t 123 -s 456`，并读取其输出。

```go
import (
  "log"
  "subproc"
)

func main() {
  msg, exit := subproc.Create("a.exe", "-t", "123", "-s", "456").Run()

  for {
    select {
    case m := <-msg:
      log.Println(m)

    case n := <-exit:
      log.Printf("Sub process exited with code: %v\n", n)
      return
    }
  }
}
```
