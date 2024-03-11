---
title: "在 Unity 中使用 MSBuild"
meta_title: ""
description: "在 Unity 中使用MSBuild来编译"
categories: ["unity"]
author: "Akaishi Toshiya"
date: 2021-09-28T16:44:24+08:00
tags: ["unity", "游戏开发相关", "MSBuild"]
draft: false
---
## 起因

众所周知，在 Unity 里面使用 Nuget 包，简直是 Nightmare。

之后又使用过 `Nuget for Unity`，本以为是棵救命稻草，但是没想到这个坑更深。

于是尝试一番 `MSBuild for Unity`，好歹也是 M$ 整出来的东西，照理说应该整合性应该会好很多把，也许。

## 安装 MSBuild for Unity

1. 为`Package Manager`添加源。
   1. `Edit` -> `Project Settings` -> `Package Manager`
   2. `New Scoped Registry`
      1. `Name`: `Microsoft`
      2. `URL`: `https://pkgs.dev.azure.com/UnityDeveloperTools/MSBuildForUnity/_packaging/UnityDeveloperTools/npm/registry/`
      3. `Scope(s)`: `"com.microsoft"`

2. 打开`${项目文件夹}\Packages\manifest.json`，在`dependencies`一节中的最后面加入：

   ```json
   {
       "dependencies": {
           "com.unity.collab-proxy": "1.9.0",
           // ... 原本就存在的一些东西
           
           "com.microsoft.msbuildforunity": "0.9.2-20200131.11" // 新加的
       }
   }
   ```

3. 回到 Unity 之后，会自动安装，等待完成。

## 编辑 {项目名称}.Dependencies.msb4u.csproj

使用 Visual Studio 打开`${项目文件夹}\Assets\{项目名称}.Dependencies.msb4u.csproj`

右键点击 C# 项目，进行安装 Nuget 包。

安装完成之后，打开 `csproj` 的编辑模式，`Ctrl + S` 保存。

之后，就可以正常编辑项目中的 C# 代码了。
