---
title: "在 vscode 中使用 prettier + eslint 格式化和规范化 ts 代码"
meta_title: ""
description: "在 vscode 中使用 prettier + eslint 格式化和规范化 ts 代码"
categories: ["ts&js"]
author: "Akaishi Toshiya"
date: 2024-07-22T17:23:00+08:00
tags: ["ts&js", "vscode", "eslint", "prettier"]
draft: true
---

本文讲述了如何在 vscode 中，使用 prettier + eslint 来格式化和规范化 js 以及 ts 代码。

> **（仅做存档） 此方法未成功。**

## 安装插件

搜索安装 `rvest.vs-code-prettier-eslint`。

根据 Prerequisites 做一下预备工作。
这里以 bun 为例。

```bash
bun add -g prettier@^3.1.0 eslint@^8.52.0 prettier-eslint@^16.1.2 @typescript-eslint/parser@^5.
0.1 typescript@^4.4.4
```

在项目中添加 `.vscode/settings.json`

```json
{
  "editor.defaultFormatter": "rvest.vs-code-prettier-eslint",
  "editor.formatOnType": false, // required
  "editor.formatOnPaste": true, // optional
  "editor.formatOnSave": true, // optional
  "editor.formatOnSaveMode": "file", // required to format on save
  "files.autoSave": "onFocusChange", // optional but recommended
  "vs-code-prettier-eslint.prettierLast": false // set as "true" to run 'prettier' last not first
}
```

重新启动 vscode。

## 配置

* eslint 使用 `.eslintrc.*` 作为配置文件。
* prettier 使用 `.prettierrc`（`json`或者`yaml`格式） 作为`.json/.yaml/.yml`的配置文件，`.prettierrc.js` 或者 `prettier.config.js`。
