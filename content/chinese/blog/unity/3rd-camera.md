---
title: "第三人称视角视角相机设定"
meta_title: ""
description: "在 Unity 中使用第三人称视角视角相机设定"
categories: ["unity"]
author: "Akaishi Toshiya"
date: 2021-09-16T11:44:29+08:00
tags: ["unity", "游戏开发相关"]
draft: false
---

## 安装 Cinamachine

> 使用菜单中的 `Window` > `Package Manager` 搜索安装。

## 创建 FreeLook 相机

> 菜单栏中的 `Cinamachine` > `Create FreeLook Camera` 

可以将创建好的游戏部件改名为`Third Person Camera`以便区分。

## 连接相机与玩家

### 相机跟随玩家

将`Third Person Player`拖拽到`Third Person Camera`部件的`CinemachineFreeLook`组件的`Follow`属性中。

### 相机看向玩家

将`Third Person Player`拖拽到`Third Person Camera`部件的`CinemachineFreeLook`组件的`Look At`属性中。

## 设定相机

设定`Third Person Camera`中，`Orbits`部分的`TopRig`、`MiddleRig`和`BottomRig`。

> `Third Person Camera`中，`Orbits`部分里，`TopRig`、`MiddleRig`和`BottomRig`分别对应最高点、中间和最低点的相机圆形轨道。

将`Binding Mode`改为`World Space`。
