---
title: "03. Android - 用 Go 来开发一个跨平台的 2D 游戏"
meta_title: ""
description: "本文讲述了如何将上一章的游戏编译成APK，本系列讲述了如何使用 Go 语言来开发一个 2D 跨平台游戏。"
categories: ["golang", "ebitengine"]
author: "Akaishi Toshiya"
date: 2024-04-30T03:21:00+08:00
tags: ["golang", "ebitengine", "游戏开发"]
draft: false
---

本期的目标是构建一个基础项目结构，最终项目是可以编译成桌面和安卓APK，并都成功显示 Ebitengine 的 Logo 为目标。

## 构建 APK

这里默认你已经准备好了基本的 Android 开发环境，包括：

- Android SDK
- Android Studio

需要注意的是，ebitengine 支持的 Android SDK 版本最低为 16。

如果你还不清楚如何搭建 Android 开发环境，可以参考 [Android 环境配置](../../android/env/)

### 新建 Android 项目

打开 Android SDK，我们选择 No Activity 项目进行创建。

![Android项目设置](../images/ebiten_android_project_settings.jpg)

由于官方给出的样例是 Java 的，这里我们也将语言设置为 Java。
注意将图片中的项目位置改为我们这次项目的地址：`<项目根目录>/mobile/android`

### 创建 ebitenmobile 绑定

```go
package mobile

import (
	"github.com/hajimehoshi/ebiten/v2/mobile"

	"github.com/toshiya14/ebiten_test/game"
)

func init() {
	g := game.New(1280, 720)
	mobile.SetGame(g)
}

func Dummy() {
	// At least one export names.
}
```

打开一个终端，并运行：

```shell
mkdir mobile/android
mkdir mobile/android/gogame
go run github.com/hajimehoshi/ebiten/v2/cmd/ebitenmobile bind -target android -javapkg github.toshiya14.ebiten_test -o ./mobile/android/gogame/gogame.aar ./mobile
```

他会创建绑定，并且输出到 `ebiten_test/mobile/android/gogame/gogame.aar`。

#### 错误解决： could not locate Android SDK

运行上面的命令时，若出现这样的错误提示，请添加环境变量 `ANDROID_HOME`，将值设置为安装在本机的 Android SDK 的位置。

如果是 Windows 环境，并且使用 Powershell 可以考虑使用下面的命令：

```powershell
$env:ANDROID_HOME="D:\Android\sdk"
```

然后再次运行 `ebitenmobile bind`。

#### 错误解决： no usable NDK

这个错误一般是因为配置 Android 环境的时候，没有安装 NDK 导致的。
使用 SDK Manager 安装 NDK 即可。

### 编辑 gradle

定位到 `ebiten_test/mobile/android/gogame`，建立一个文件 `build.gradle`。

```build.gradle
configurations.maybeCreate("default")
artifacts.add("default", file('gogame.aar'))
```

### 添加模块

在项目根目录中的 `settings.gradle` 中，将最后一行 `include :app` 改为：

```settings.gradle
include :app :gogame
```

在 `app` 项目的 `build.gradle` 的 `dependencies` 中加入：

```build.gradle
implementation project(":gogame")
```

### 新增 MainActivity

在 `app` 项目中，我们新建一个空白的 Activity，叫 `MainActivity`，将布局名设置为 `activity_main` 并写入以下内容：

```java
package github.toshiya14.ebiten_test;
import androidx.appcompat.app.AppCompatActivity;
import android.os.Bundle;
import go.Seq;
import github.toshiya14.ebiten_test.mobile.EbitenView;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Seq.setContext(getApplicationContext());
    }

    private EbitenView getEbitenView() {
        return (EbitenView)this.findViewById(R.id.ebitenview);
    }

    @Override
    protected void onPause() {
        super.onPause();
        this.getEbitenView().suspendGame();
    }

    @Override
    protected void onResume() {
        super.onResume();
        this.getEbitenView().resumeGame();
    }
}
```

之后，再打开 `layout` 中的 `activity_main.xml`，修改为：

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:keepScreenOn="true"
    tools:context=".MainActivity">
<github.toshiya14.ebiten_test.mobile.EbitenView
    android:id="@+id/ebitenview"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:focusable="true"/>
</RelativeLayout>
```

### 修改 AndroidMenifest

定位到 `app/src/main/AndroidMenifest.xml`
如果在 Android Studio 中，并且加载了 Gradle 的情况下，则导航到 `app/manifests/AndroidMenifest.xml`。

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android">

    <uses-feature
        android:glEsVersion="0x00020000"
        android:required="true" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity
            android:name=".MainActivity"
            android:exported="true"
            android:screenOrientation="landscape">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```

### 添加 styles

定位到 `app/values/styles.xml`

```xml
<resources>

    <!-- Base application theme. -->
    <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
        <!-- Customize your theme here. -->
        <item name="colorPrimary">@color/colorPrimary</item>
        <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
        <item name="colorAccent">@color/colorAccent</item>
        <item name="windowNoTitle">true</item>
        <item name="android:windowFullscreen">true</item>
        <item name="android:windowActionBar">false</item>
        <item name="android:windowContentOverlay">@null</item>
    </style>
</resources>
```

## 编译运行

直接使用 Android Studio 编译运行一下看看结果吧。
