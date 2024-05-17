---
title: "02. 嵌入资源 - 用 Go 来开发一个跨平台的 2D 游戏"
meta_title: ""
description: "本文讲述了如何将从文件读取图片，改为程序内置资源，本系列讲述了如何使用 Go 语言来开发一个 2D 跨平台游戏。"
categories: ["golang", "ebitengine"]
author: "Akaishi Toshiya"
date: 2024-04-30T03:21:00+08:00
tags: ["golang", "ebitengine", "游戏开发"]
draft: false
---

上一章我们实现了一个简单的游戏程序，显示了游戏目录中的一个图片。
这一章我们会把图片内嵌到我们的程序中，然后从程序内部读取。

这也是为我们后面生成移动端游戏做准备。

## 准备资源

在项目中新建一个文件夹：`assets`。
再新建一个 `images`，将 `logo.png` 放入该文件夹。

```text
📁 assets
|-- 📁 images/
|-- | -- 📄 logo.png
|-- 📄 assets.go
|-- 📄 loader.go
```

## 声明嵌入资源

将下面的代码写入 `assets.go`

```go
package assets

import (
	"embed"
)

//go:embed images/*
var Assets embed.FS
```

这样一来，go 在编译的时候，就会将 `images/*` 中的文件都作为嵌入资源，写入编译后的程序中。

## Loader

将下面的代码写入 `loader.go`

```go
package assets

import (
	"image"
	"path"

	"github.com/hajimehoshi/ebiten/v2"
)

func LoadImage(name string) (*ebiten.Image, error) {
	f, err := Assets.Open(path.Join("images", name))
	if err != nil {
		return nil, err
	}
	defer f.Close()

	img, _, err := image.Decode(f)
	if err != nil {
		return nil, err
	}

	return ebiten.NewImageFromImage(img), nil
}
```

我们就可以使用 `assets.LoadImage("logo.png")` 来读取 `logo.png` 文件了。


## 改变图片读取方式

回到项目根目录的 `game/game.go` 中。

将

```go
img, _, err := ebitenutil.NewImageFromFile("logo.png")
```

改为

```go
img, err := assets.LoadImage("logo.png")
```

## 测试

运行以下测试程序是否正常运行。
