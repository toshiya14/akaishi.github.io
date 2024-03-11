---
title: "Go + VSCode 开发环境准备"
meta_title: ""
description: "本文讲述了如何准备 golang + vscode 进行开发的步骤。"
categories: ["golang"]
author: "Akaishi Toshiya"
date: 2024-01-21T01:00:00+08:00
tags: ["golang", "vscode"]
draft: false
---

## 1. 安装 Go 环境

通过 `scoop` 安装，直接输入 `scoop install go`。
如果想要手动安装，可以去 [golang 官方下载页面](https://go.dev/dl/) 下载安装。

## 2. 安装 vscode

通过 `scoop` 安装，直接输入 `scoop install vscode`。
如果想要手动安装，可以去 [vscode 官方下载页面](https://code.visualstudio.com) 下载安装。

## 3. 安装 vscode 插件

点击 vscode 中的插件选项卡，搜索 `go`，安装。

## 4. 若 go get 无法连接服务器

运行

```shell
go env -w GO111MODULE=on
go env -w GOPROXY=https://goproxy.cn,direct
```

## 5. Hello world

1. 创建一个文件夹，用来存放我们的源代码，比如`hello-world`，需要注意的是，尽量使用小写字母和连字符，不要使用中文和空格。
2. 打开命令提示符，导向新建的文件夹。
3. 运行 `go mod init hello_world`，初始化项目。
4. 在新建的文件夹内，右键点击空白处，选择**通过 Code 打开**。
5. 在 Visual Studio Code 中，创建一个新文件，叫作 `main.go`。
6. 输入以下代码：
   ```go
   package main

   import "fmt"

   func main() {
      fmt.Println("Hello World")
   }
   ```

### 程序测试

打开命令提示符，输入 `go run main.go`，就会从 `main.go` 里面去寻找 `main` 函数，作为入口函数执行。

### 程序编译

打开命令提示符，输入 `go build`。
