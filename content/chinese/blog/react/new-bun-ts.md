---
title: "创建 bun + ts + react 项目"
meta_title: ""
description: "本文讲述了如何创建一个新的 bun + ts + react 项目，以及其他库的引用。"
date: 2023-12-29T04:44:00+08:00
categories: ["web前端", "react"]
author: "Akaishi Toshiya"
tags: ["web", "react", "spa"]
draft: false
---

最近好像 bun 突然很火的样子，于是想着研究一下替换掉原来的 node + pnpm 的工作流，会不会更加易用一些呢。

## 安装配置 bun

```bash
curl -fsSL https://bun.sh/install | bash
```

如果出现访问不通畅的问题，可以给 curl 加代理

```bash
export https_proxy=http://172.27.32.1:1080
curl -fsSL https://bun.sh/install | bash
export https_proxy= #关闭代理
```

MacOS 以及 Linux 也可以通过 brew 安装

```bash
brew install oven-sh/bun/bun
```

对于 Windows 用户，可以通过 powershell

```shell
irm bun.sh/install.ps1 | iex
```

如果你配置了 scoop，也可以通过

```shell
scoop install bun
```

## 基础 react 项目构建

```bash
bun create vite <project_name>
cd <project_name>
bun install
```

## 引入 UI 库

下面以 ChakraUI 和 styled-components 为例，可以根据需要选择添加。

另外还有一些额外的 UI 库：

* react-icons （react 图标整合库）
* json-edit-react （JSON 编辑器）

### ChakraUI

```bash
bun add @chakra-ui/react @emotion/react @emotion/styled framer-motion
```

### styled-components

TODO:（待添加）

### 其他 UI 库

#### react-icons

```bash
bun add react-icons
```

#### json-edit-react

## 引入路由 react-router

## 引入 sass 样式编译器
