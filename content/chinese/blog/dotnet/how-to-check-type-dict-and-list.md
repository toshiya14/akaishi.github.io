---
title: "如何推断一个实例是否是 Dictionary 或者 List 集合类型"
meta_title: ""
description: "如何推断一个实例是否是 Dictionary 或者 List 集合类型。"
categories: ["dotnet", "ASP.NET", "System.Reflection"]
author: "Akaishi Toshiya"
date: 2022-05-24T21:05:27+08:00
tags: [".NET", "ASP.NET", "反射"]
draft: false
---

## 判定一个实例是否是 Dictionary

```csharp
using System.Collections;

var type1 = new Dictionary<string, int>().GetType();
var isDict = type1.IsGenericType && typeof(IDictionary).IsAssignableFrom(type1);
Console.WriteLine($"isDict: {isDict}");
// output: isDict: True
```

## 判定一个实例是否是 List

```csharp
var listType1 = new List<string>().GetType();
var isList1 = listType1.IsGenericType && typeof(IList).IsAssignableFrom(listType1);
var listType2 = new byte[14].GetType();
var isList2 = listType2.IsGenericType && typeof(IList).IsAssignableFrom(listType2);
var listType3 = new List<string>().AsEnumerable().GetType();
var isList3 = listType3.IsGenericType && typeof(IList).IsAssignableFrom(listType3);

Console.WriteLine($"isList: {isList1}, {isList2}, {isList3}");
// output: isList: True, False, True
```

由此可见，`Array` 类型的不能经由这个方法进行判定。如果要包含所有派生自 `IEnumerable` 的类型，可以考虑通过 `IEnumerable` 类型来判定。
