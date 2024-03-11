---
title: "WSL 高内存使用率的解决方法"
meta_title: ""
description: "WSL 高内存使用率的解决方法"
categories: ["杂七杂八"]
author: "Akaishi Toshiya"
date: 2023-02-13T14:55:06+08:00
tags: ["wsl"]
draft: false
---

## 参考

> https://github.com/microsoft/WSL/issues/8725

## 步骤

1. 在 `C:\Users\<用户名缩写文件夹>`，创建 `.wslconfig` 文件，如果已经存在，则编辑他。
  **可以通过环境变量 `%USERPROFILE%` 直接跳转到该文件夹。**
2. 编辑文件：
  ```ini
  [wsl2]
  memory=1GB # Limits VM memory in WSL 2. If you want less than 1GB, use something like 500MB, not 0.5GB
  processors=2 # Makes the WSL 2 VM use two virtual processors
  ```
  请确认该文件以 `UTF-8` 编码保存，并且将行尾设置为 `LF`，据说好像 `CRLF` 也行。
3. 在 `Powershell(管理员)` 中退出 Docker，运行：`Restart-Service LxssManager`。
4. 重新运行 Docker 或者 wsl，在 wsl 内，运行 `free -mh` 然后确认 `total` 的值，应该是我们上面设置的 `1GB` 了。
