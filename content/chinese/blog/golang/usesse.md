---
title: "Gin 中使用 SSE (Server Sent Events)"
meta_title: ""
description: "本文讲述了如何在 gin 中处理 SSE。"
categories: ["golang", "gin"]
author: "Akaishi Toshiya"
date: 2024-04-06T16:04:00+08:00
tags: ["golang", "gin", "sse"]
draft: false
---

SSE 是一种 Server Push 技术的实现，通过发送请求到服务器之后，服务器阶段性地进行回复，
来实现一种好像是服务器在主动发送消息的样子。
该请求的响应内容 mimetype 为 `text/event-stream`。
通常和普通的 GET 请求一样，先由客户端发起请求，服务器返回：
```
data:<数据>\r\n
```
只有当 header 为 `data:` 的时候，当前行的内容会被认为是一个数据类型，SSE 也支持一些其他类型的消息，此处就不展开了。

## 伸手党福利

你可以从下面的位置获取源代码。

[usesse - Gists - Akaishi Toshiya](https://gist.github.com/toshiya14/9aaa969df17006688b50b65d88232ff1)

`example.go` 示例了如何使用它，你真正需要导入项目里面的只有 `UseSSE.go`。

## 说明

1. 传入 `gin.Context` 对象，设置回应的头：
   * Content-Type: text/event-stream
   * Cache-Control: no-cache
   * Connection: keep-alive
2. 返回一个 `SSEContext` 对象，通过这个对象可以对客户端进行：
   * 推送数据
   * 关闭连接
3. 在请求的主线程中，应当启用一个新的 go-routine，并立刻调用 `SSEContext` 的 `Wait()` 方法进入等待和处理。
   在新的 go-routine 中，可以调用 `PushData()` 方法进行数据的推送。如果调用 `CloseData()`，则会关闭 SSE。
   原先的 `Wait` 也不再等待，将最后的数据 flush 之后就会退出。
