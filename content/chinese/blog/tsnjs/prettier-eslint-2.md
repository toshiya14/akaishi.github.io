---
title: "在 vscode 中使用 prettier + eslint 格式化和规范化 ts 代码"
meta_title: ""
description: "在 vscode 中使用 prettier + eslint 格式化和规范化 ts 代码"
categories: ["ts&js"]
author: "Akaishi Toshiya"
date: 2024-07-22T17:23:00+08:00
tags: ["ts&js", "vscode", "eslint", "prettier"]
draft: false
---

本文讲述了如何在 vscode 中，使用 prettier + eslint 来格式化和规范化 js 以及 ts 代码。

## 安装插件

搜索安装：

* dbaeumer.vscode-eslint
* esbenp.prettier-vscode

准备工作，需要安装 eslint 在项目中，或者全局安装。

```bash
bun add -g eslint
```

## 配置

settings.json

```json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "eslint.enable": true,
  "[javascriptreact]": {
    "editor.codeActionsOnSave": {
      "source.organizeImports": true,
      "source.fixAll.eslint": true
    }
  },
  "[javascript]": {
    "editor.codeActionsOnSave": {
      "source.organizeImports": true,
      "source.fixAll.eslint": true
    }
  },
  "[typescript]": {
    "editor.codeActionsOnSave": {
      "source.organizeImports": true,
      "source.fixAll.eslint": true
    }
  },
  "[typescriptreact]": {
    "editor.codeActionsOnSave": {
      "source.organizeImports": true,
      "source.fixAll.eslint": true
    },
  },
}
```

## 解决 eslint 和 prettier 共存

安装 node 包：

* eslint-plugin-prettier
* eslint-config-prettier

```bash
bun add -D eslint-config-prettier eslint-plugin-prettier
```

.eslintrc.cjs

```js
extends: [..., "plugin:prettier/recommended"]
rules: {
    ...,
    "prettier/prettier": "error"
}
```

.prettierrc.cjs

```js
module.exports = {
    semi: false,
    singleQuote: true,
    bracketSpacing: true,
}
```
