---
title: "Docker问题修复：failed to read metadata"
meta_title: ""
description: "运行 docker build xxx 时，如果出现 failed to read metadata 的解决方法。"
categories: ["杂七杂八"]
author: "Akaishi Toshiya"
date: 2023-02-13T14:55:06+08:00
tags: ["docker"]
draft: false
---

运行 `docker build xxx` 时，如果出现 

>  failed to read metadata: unexpected end of JSON input

可以尝试删除` ~/.docker/contexts`，让引擎重新创建。

如果删除之后，提示

> failed to solve: error getting credentials - err: docker-credential-desktop.exe resolves to executable in current directory (./docker-credential-desktop.exe)

打开 `~/.docker/config.json`，删除 `credStore` 片段。
