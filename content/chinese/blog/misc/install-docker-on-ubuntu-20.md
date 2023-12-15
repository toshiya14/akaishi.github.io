---
title: "Ubuntu 20.04 安装 Docker 环境"
meta_title: ""
description: "本文讲述了如何在 Ubuntu 20.04 安装 Docker 环境"
date: 2023-12-15T16:44:30+08:00
categories: ["杂七杂八"]
author: "Akaishi Toshiya"
tags: ["环境配置", "Docker"]
draft: false
---

> 本文参考了官方文档，并添加了部分内容。
> 原文链接：[https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/)

## 卸载非官方的包

下面的包都是非官方的，为了防止冲突，需要卸载掉。

* docker.io
* docker-compose
* docker-compose-v2
* docker-doc
* podmon-docker

而且，Docker 也依赖 `containerd` 和 `runc`，所以需要卸载掉，因为 Docker 已经把他们都打包成 `containerd.io` 了。

```bash
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

## 添加官方 GPG 密钥

```bash
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

## 添加 Docker 仓库

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

## 安装 Docker

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

## 测试是否完成安装

```bash
sudo docker run hello-world
```
