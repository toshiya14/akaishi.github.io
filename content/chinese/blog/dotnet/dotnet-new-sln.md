---
title: "使用 dotnet 命令新建一个解决方案"
meta_title: ""
description: "本文讲述了如何使用 dotnet 命令来创建解决方案"
categories: ["dotnet", "ASP.NET"]
author: "Akaishi Toshiya"
date: 2024-11-26T15:11:00+08:00
tags: [".NET", "ASP.NET"]
draft: false
---

## 创建 sln

```shell
dotnet new sln -n Solution1
```

`Solution1` 为解决方案的名称。

## 创建项目

假定这个解决方案包含了三个项目，类型分别是 `classlib`、`console` 和 `wpf`。

```shell
dotnet new classlib -n Solution1.Lib
dotnet new console -n Solution1.Console
dotnet new wpf -n Solution1.GUI
```

完成上面的操作之后，当前目录下的结构如下：

- `Solution1.sln` - 解决方案文件
- `Solution1.Lib` - 📁 一个类库项目
- `Solution1.Console` - 📁 一个控制台项目
- `Solution1.GUI` - 📁 一个 WPF 项目

## 添加到 sln

到目前位置，上面的操作都只是新建了三个项目，还没有把他们添加到 `Solution1.sln` 中。
此时如果你打开 `Solution1.sln`，你会发现里面一个项目都没有。

对于 powershell，可以执行下面的语句：

```powershell
Get-ChildItem -Directory -Path "./Solution1.*" | ForEach-Object { dotnet sln add "./$($_.Name)" }
```

上面的语句指定了 `-Path "./Solution1.*"`，表示会将 `Solution1.` 开头的任意文件夹内的项目添加至当前目录的 sln 中。

如果当前目录下只有一个 `sln` 文件，执行 `dotnet sln add xxx` 的时候，就会添加到这个 `sln` 文件中。
如果当前目录下包含多个 `sln` 文件，则需要使用 `dotnet sln xxx.sln add xxx` 显式指定 `sln` 文件。

