---
title: "VSCode 插件开发随机：插件如何处理浏览器回调"
meta_title: ""
date: 2023-12-13T02:24:30+08:00
categories: ["vscode", "extensions"]
author: "Akaishi Toshiya"
tags: ["vscode", "vscode插件"]
draft: false
---

> 原文： [How to callback to your extension from outside Visual Studio Code](https://www.eliostruyf.com/callback-extension-vscode/)

## 场景分析

有的时候，我们可能需要打开浏览器到登录页面，然后进行授权鉴权，下发一个 token。
那我们怎么才能回传这个 token 到 vscode 中呢？

在执行 `activate()` 的时候，可以调用 `registerUriHandler` 来注册一个 `vscode` 链接的处理方法。

浏览器发往 `vscode://<publisher>.<extension name>` 的请求都会经由这个注册的方法来处理。

## 实现方法

这里以一个很简单的例子，比如传入一个 `say` 参数，如果插件接到请求之后，显示 `say` 中的内容。

在 `activate()` 中，我们可以写入下面的内容。

```typescript
import * as vscode from 'vscode';

export function activate(context: vscode.ExtensionContext) {

  const handleUri = (uri: vscode.Uri) => {
  const queryParams = new URLSearchParams(uri.query);

    if (queryParams.has('say')) {
      vscode.window.showInformationMessage(`URI Handler says: ${queryParams.get('say') as string}`);
    }
  };

  context.subscriptions.push(
    vscode.window.registerUriHandler({
      handleUri
    })
  );
}

export function deactivate() {}
```

这里我们定义了一个 `handleUri` 方法，然后使用 `vscode.window.registerUriHandler` 来注册它。

从浏览器中访问：`vscode://pub.extname?say=Hello+World`。
上面的 `pub` 和 `extname` 对应改成你的插件的发布者和插件名。

正常情况下，vscode 中应该会出现一个弹窗，显示的内容就是 `Hello World`。
