---
title: "如何配置 Android 开发环境"
meta_title: ""
description: "本文讲述了如何在 Windows 平台上配置开发环境"
categories: ["Android"]
author: "Akaishi Toshiya"
date: 2024-04-30T04:51:00+08:00
tags: ["Android"]
draft: false
---

## 安装 Java

[Download Java SDK](https://www.oracle.com/java/technologies/downloads/)

选择 Windows 平台，下载并安装。

设置以下环境变量：

* `JAVA_HOME`: 刚刚安装的位置
* `PATH`: 最后添加 `%JAVA_HOME%\bin`

## 下载 Android Studio

从官方下载 [Android Studio](https://developer.android.com/studio?hl=zh-cn)。

中国大陆地区可以访问 [Android Studio](https://developer.android.google.cn/studio?hl=zh-cn) 加速下载。

安装之后运行。

## 配置

根据向导，来到 **Install Type**。
我们选择 **Custom**

### 加速器配置

此时，会弹出一个对话框，提示 **Unable to access Android SDK add-on list**
这里需要自备加速器。

选择 **Set Proxy** 设置加速器。

需要注意的是，加速器设置中，可以把 `dl.google.com` 加入不代理的域名。

对，这个域名是不用加速也可以很快进行下载的。

### SDK 安装

点击下一步会来到 **SDK Components Setup**

这一步选择好 SDK 的安装位置，其他我们保持原有默认选项。

![SDK Components Setup](../images/sdk-components-dialogue.jpg)

-----

后面的步骤只需一路下一步，在最后的许可证的位置，选择 **Accept**，并点击 **Finish** 即可。

### 等待下载和安装完成

RT

### 其他可选项

根据你自己的开发需要，可以打开 SDK 安装其他开发依赖。
比如 NDK， CMake 等。
