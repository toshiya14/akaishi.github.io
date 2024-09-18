---
title: "问题修复：在 vscode 中启动 pnpm 单开窗口"
meta_title: ""
description: "问题修复笔记"
date: 2023-12-29T04:44:00+08:00
categories: ["杂七杂八"]
author: "Akaishi Toshiya"
tags: ["vscode", "pnpm", "node"]
draft: false
---

开发过程中，遇到这么一个问题。

如果在 vscode 里面运行 pnpm 命令，会单独开启一个窗口，而不是在 vscode 的终端里面开启任务。

如果是配置在 tasks.json 里面的任务，则不会停止等待依赖的任务完成，就开始了下一个任务。

> https://github.com/pnpm/pnpm/issues/5834

这里给出了解决方法。

## 在 vscode 中强制安装 pnpm

```shell
npm i -g pnpm --force
```

在 vscode 中打开终端，运行这个命令。

然后你就会发现，问题解决了。

具体原因暂时未知。
