---
title: "开发 .NET Standard 库时，使用更新版本的 C# 语言特性"
meta_title: ""
description: "本文讲述了，如何在开发 .NET Standard 2.1 或者更老的库时，使用较新的 C# 语言版本和特性。"
date: 2024-03-21T03:11:23+08:00
categories: ["dotnet", "C#"]
author: "Akaishi Toshiya"
tags: [".NET", "C#"]
draft: false
---

也许你也遇到过这种情况，就是需要保证兼容性的最大化，被迫使用比较老的 .NET 版本，比如 .NET Standard 2.1。
但是又习惯了比较新的 C# 语法，例如：

* 文件范围的 namespace
* record 类型
* required 成员
* Index 和 Range
* ...

但是大部分新特性在你编写 .NET Standard 2.1 的库时，会提示你这个语言特性在 C# 8.0 中不支持，需要使用 C# x.0 以上版本才行。
有一个库，就可以让我们在这种情况下使用更新的语言特性。

> [PolySharp](https://github.com/Sergio0694/PolySharp)

## 编辑 csproj

```xml
<PropertyGroup>
  <TargetFrameworks>netstandard2.1;net6.0</TargetFrameworks>
  <ImplicitUsings>enable</ImplicitUsings>
  <Nullable>enable</Nullable>
  <LangVersion>12.0</LangVersion>
</PropertyGroup>
```

如上图，重点是加入 `<LangVersion>`，并指定你想用的版本，这里以 12.0 为例。

## 安装 PolySharp

以你喜欢的方式，安装 nuget 包： `PolySharp`。

## 开始 Coding 吧

加油~！
