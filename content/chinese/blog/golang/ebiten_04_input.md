---
title: "04. 输入 - 用 Go 来开发一个跨平台的 2D 游戏"
meta_title: ""
description: "本文讲述了如何在我们的游戏中检测输入，本系列讲述了如何使用 Go 语言来开发一个 2D 跨平台游戏。"
categories: ["golang", "ebitengine"]
author: "Akaishi Toshiya"
date: 2024-05-01T00:13:00+08:00
tags: ["golang", "ebitengine", "游戏开发"]
draft: true
---

本期的目标是在我们的游戏中检测输入，并做出响应的反应。
那我们就先以一个简单的例子来看吧。

当用户点击屏幕或者鼠标点击游戏画面时，上一章我们做好的 图片会变为原来的 1.5 倍大小。
再次点击时，缩放回 1.0 倍大小。

## 
