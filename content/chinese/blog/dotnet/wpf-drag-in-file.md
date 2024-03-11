---
title: "在 WPF 拖入文件的处理"
meta_title: ""
description: "本文讲述了如何使用自签名证书在 ASP.NET Web 项目中进行客户端证书的验证。"
categories: ["dotnet", "WPF"]
author: "Akaishi Toshiya"
date: 2021-10-09T05:10:06+08:00
tags: [".NET", "WPF"]
draft: false
---

```c#
private void Window_Drop(object sender, DragEventArgs e)
{
    if (e.Data.GetDataPresent(DataFormats.FileDrop))
    {
        if (e.Data.GetData(DataFormats.FileDrop) is string[] files && files.Length > 0)
        {
            var filepath = files[0];
            // do something to files;
        }
    }
}
```

* 如函数名，挂载在 `Window` 的 `Drop` 事件上。
* 如果需要限定在其他范围内的话，应该是挂载在其他控件的相同事件上。
* `files` 包含了所有拖入的文件，如果你拖入多个文件，就会有多个元素。
