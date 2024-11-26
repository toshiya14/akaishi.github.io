---
title: "你的第一个 vscode 插件"
meta_title: ""
description: "本文讲述了如何初始化一个 vscode 插件项目。"
categories: ["vscode-ext"]
author: "Akaishi Toshiya"
date: 2023-02-13T14:55:06+08:00
tags: ["vscode扩展"]
draft: false
---

> 原文：[Your First Extension](https://code.visualstudio.com/api/get-started/your-first-extension)

## 依赖

* Node.js
* Git

## 准备工作

```bash
npm install --global yo generator-code
yo code
```

之后会出现一个引导，配置一些相关的信息，这里我们需要使用 ts 进行开发，所以回答如下：

```bash
# ? What type of extension do you want to create? New Extension (TypeScript)
# ? What's the name of your extension? HelloWorld
### Press <Enter> to choose default for all options below ###

# ? What's the identifier of your extension? helloworld
# ? What's the description of your extension? LEAVE BLANK
# ? Initialize a git repository? Yes
# ? Bundle the source code with webpack? No
# ? Which package manager to use? npm

# ? Do you want to open the new folder with Visual Studio Code? Open with `code`
```

完成上述的步骤之后，等待完成，选择使用 code 打开项目文件夹。

## 文件结构说明

`src/extension.ts` 为主要入口文件。

