---
title: "VSCode + WSL 开发"
meta_title: ""
description: "本文讲述了如何使用 VSCode 的远程连接到本地的 WSL 上进行开发"
date: 2023-12-13T02:24:30+08:00
categories: ["杂七杂八"]
author: "Akaishi Toshiya"
tags: ["vscode", "wsl"]
draft: false
---

## 准备工作

本文假定你的环境满足以下条件：

* 已安装 wsl2，本例以 Ubuntu 24.04 发行版作为例子
* wsl 中已安装 go 语言环境（也可以是其他环境，只是本文最终以 go 跑通一个 Hello World 为例）

## 墙外方法

如果你处于墙外，可以直接启动 vscode 的 WSL 远程模式，vscode 会自动安装所需要的 `code-server`。

```shell
code --remote wsl+Ubuntu-24.04 "$remote_path"
```

启动之后，vscode 应该会自动进行 `code-server` 的安装。

## 手动方法

如果你处于墙内，大概率会因为网络问题，最终导致安装 `code-server` 失败。
或者上一步中，自动安装过程失败了。

你就需要看看使用手动部署的方法安装 `code-server`。

> 该部分参考了文章：
> * [wsl + vscode 离线配置 ERROR: Faild to download https://update.code.visualstudio.com](https://blog.csdn.net/weixin_40653140/article/details/135996089)

### 1. 下载 code-server 离线包

在这一步中，我们首先需要知道你当前 vscode 的 commit 版本号。
点击 vscode 中的 「帮助」-「关于」，可以看到以下信息：

```plaintext
版本: 1.92.2
提交: fee1edb8d6d72a0ddff41e5f71a671c23ed924b9
日期: 2024-08-14T17:29:30.058Z
Electron: 30.1.2
ElectronBuildId: 9870757
Chromium: 124.0.6367.243
Node.js: 20.14.0
V8: 12.4.254.20-electron.0
OS: Windows_NT x64 10.0.19044
```

我们需要关注的是 **提交** 那一栏，这里是 `fee1edb8d6d72a0ddff41e5f71a671c23ed924b9`。

由于我们的 wsl 是使用了 linux-x64，对应的我们需要下载的 `code-server` 为：

```
https://update.code.visualstudio.com/commit:fee1edb8d6d72a0ddff41e5f71a671c23ed924b9/server-linux-x64/stable
```

这里需要将 `fee1edb8d6d72a0ddff41e5f71a671c23ed924b9` 的部分对应的改成你的 commit 版本。

### 2. 解包

将上一步下载好的 `vscode-server-linux-x64.tar.gz` 放置到 wsl 的 ~ 目录下。
我们可以通过 Windows 的资源管理器，访问 `\\wsl$\Ubuntu+24.04\home\toshi`。
注意，这个位置，可能也需要将 `Ubuntu+24.04` 改为你安装的 linux 发行版，`toshi` 对应改成你的 wsl 中的用户名。
如果你不是太确定怎么填写，也可以在资源管理器中进入 `\\wsl$` 之后，一步一步操作。
**特别注意不要搞错斜杠的方向**

拷贝完成之后，运行解包：

```shell
# 删除旧的 code-server
rm -rf ~/.vscode-server/bin

# 解压
mkdir ~/.vscode-server/bin
tar zxvf ~/vscode-server-linux-x64.tar.gz -C ~/.vscode-server/bin/
cd ~/.vscode-server/bin
mv vscode-server-linux-x64 fee1edb8d6d72a0ddff41e5f71a671c23ed924b9 # 替换 commitid

# 清理
rm -f ~/vscode-server-linux-x64.tar.gz
```

