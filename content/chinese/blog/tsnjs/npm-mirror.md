---
title: "自建 npm 镜像服务器"
meta_title: ""
description: "本文讲述了如何使用 verdaccio 建立一个 npm 镜像服务器"
categories: ["ts&js"]
author: "Akaishi Toshiya"
date: 2021-09-16T11:44:29+08:00
tags: ["ts&js", "镜像服务器", "verdaccio"]
draft: false
---

本文讲述了如何使用 verdaccio 来搭建一个 npm 的镜像服务器来加速包安装。
理论上来说，verdaccio 不光可以做镜像服务器，也可以作为私立 npm 仓库来使用。

## 安装 verdaccio

```bash
npm install -g verdaccio
yarn global add verdaccio

#.. 或者其他 npm 包管理器，安装全局包 verdaccio
```

## 创建配置

如果不指定参数启动 verdaccio，将会使用 `~/.config/verdaccio/config.yaml`，你可以将下面的内容放到这个文件内。
也可以随意放在自己想要的位置，启动时，加上 `-c <config_path>` 来指定使用哪个配置文件。

```yaml
storage: ./storage
uplinks:
  npmjs:
    url: https://registry.npmjs.org/

packages:
  "@*/*":
    access: $all
    publish: $authenticated
    proxy: npmjs
  "**":
    access: $all
    publish: $authenticated
    proxy: npmjs

log: { type: stdout, format: pretty, level: http }
http_proxy: http://localhost:2334
https_proxy: http://localhost:2334

```

### 说明

* `uplinks` 段中，我们定义 npmjs 的上游是 `https://registry.npmjs.org`，如果你希望你的上游是某个国内服务器，也可以替换掉。
* `packages` 段中，我们定义了 `@*/*` 和 `**`（任意）两种格式的包路径分别使用哪种配置。
  * `access` 指定了访问授权，`publish` 指定了发布授权。这两个字段都可以填入：
    * `$authenticated` 仅认证用户，非认证用户会返回 401 错误。
    * `$all` 表示所有用户，包括匿名的用户。
  * `proxy` 指定了这一部分的包走哪一个上游（在 `uplinks` 中定义的 `key`）。
* `http_proxy` 和 `https_proxy` 定义了在请求上游时，使用哪个代理服务器（加速用）。

## 使用

### 启动 verdaccio

```bash
verdaccio -l 4000 -c config.yaml
```

注意这里的 `-l` 参数，表示 verdaccio 会监听哪个端口的请求，如果没有指定的话，默认使用 `4873`；
`-c` 参数指定了 verdaccio 会使用哪个配置文件，默认是 `~/.config/verdaccio/config.yaml`。

### 更改 registry

不同的包管理器可能不一样。

#### npm

```bash
npm config set registry http://localhost:4000/
```

#### bun

`~/.bunfig.toml`

```ini
[install]
registry = "http://localhost:4000/"
```

然后就可以 npm install 了。

## 认证授权

TODO: 待添加
