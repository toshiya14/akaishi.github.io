---
title: "01. Windows & Hello world - 用 Go 来开发一个跨平台的 2D 游戏"
meta_title: ""
description: "本文讲述了如何实现一个基本的游戏，显示一张图片，本系列讲述了如何使用 Go 语言来开发一个 2D 跨平台游戏。"
categories: ["golang", "ebitengine"]
author: "Akaishi Toshiya"
date: 2024-04-30T03:21:00+08:00
tags: ["golang", "ebitengine", "游戏开发"]
draft: false
---

本期的目标是构建一个基础项目结构，最终项目是可以编译成桌面和安卓APK，并都成功显示 Ebitengine 的 Logo 为目标。


## 参考文章

- [Hello,world - Ebitengine](https://ebitengine.org/en/tour/hello_world.html)
- [Render an image - Ebitengine](https://ebitengine.org/en/tour/image.html)
- [Mobile - Ebitengine](https://ebitengine.org/en/documents/mobile.html)

## 最终项目文件结构

主要的结构大概如下图

```text
📁 ebiten_test
|-- 📁 assets/ -> 嵌入游戏的资源
|-- | -- 📁 images/ -> 图片资源
|-- | -- ...
|-- 📁 game/ -> 用于存放游戏对象和相关的逻辑
|-- | -- 📄 main.go -> 游戏入口
|-- 📁 mobile/ -> 用于存放移动平台相关的一些配置和项目
|-- | -- 📄 mobile.go -> 移动平台绑定
|-- 📄 main.go -> Windows 的入口
```

## Windows 入口

```bash
cd ebiten_test
go mod init github.com/toshiya14/ebiten_test
go get github.com/hajimehoshi/ebiten/v2
go get github.com/hajimehoshi/ebiten/ebitenutil
```

用你熟悉的 IDE 打开该文件夹。新建文件命名为 `main.go`。

```go
package main

import (
	"log"

	"github.com/hajimehoshi/ebiten/v2"
	"github.com/toshiya14/ebiten_test/game"
)

const (
	WIN_WIDTH  = 1280
	WIN_HEIGHT = 720
)

func main() {
	g := game.New(WIN_WIDTH, WIN_HEIGHT)
	ebiten.SetWindowSize(WIN_WIDTH, WIN_HEIGHT)
	ebiten.SetWindowTitle("Ebitengine Test Example")
	if err := ebiten.RunGame(g); err != nil {
		log.Fatal(err)
	}
}

```

## game 项目

在项目文件夹中新建一个 `game` 文件夹，并将下面的代码写入 `game.go`。

```go
package game

import (
	_ "image/png"
	"log"

	"github.com/hajimehoshi/ebiten/v2"
	"github.com/hajimehoshi/ebiten/v2/ebitenutil"
)

type Game struct {
	layoutWidth  int
	layoutHeight int
	logo         *ebiten.Image
	logoOpt      *ebiten.DrawImageOptions
}

func (g *Game) Update() error {
	return nil
}

func (g *Game) Draw(screen *ebiten.Image) {
	screen.DrawImage(g.logo, g.logoOpt)
}

func (g *Game) Layout(outsideWidth, outsideHeight int) (screenWidth, screenHeight int) {
	return g.layoutWidth, g.layoutHeight
}

func New(width int, height int) *Game {
	g := &Game{
		layoutWidth:  width,
		layoutHeight: height,
	}

	img, _, err := ebitenutil.NewImageFromFile("logo.png")
	if err != nil {
		log.Fatal(err)
	}
	g.logo = img

	// Place the image center of the screen.
	iw := g.logo.Bounds().Dx()
	ih := g.logo.Bounds().Dy()

	g.logoOpt = &ebiten.DrawImageOptions{}
	g.logoOpt.GeoM.Translate(-float64(iw)/2, -float64(ih)/2)
	g.logoOpt.GeoM.Translate(float64(g.layoutWidth)/2, float64(g.layoutHeight)/2)

	return g
}

```

运行 `go run .`，应该可以看到一个窗口中间显示的是 ebitengine 的 logo。
这就说明成功了。
