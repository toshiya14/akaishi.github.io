---
title: "WebAPI 是什么"
meta_title: ""
description: "本文讲述了 WebAPI 是什么"
date: 2023-12-29T04:44:00+08:00
categories: ["杂七杂八"]
author: "Akaishi Toshiya"
tags: ["webapi", "http"]
draft: false
---

WebAPI 是一种让服务器可以向外提供数据的接口。

一个 WebAPI 的完整流程应该是：

1. 由客户端发起请求
2. 服务器进行处理
3. 服务器将处理结果返回给客户端

## 请求的组成

```
POST https://example.com/api?key1=a&key2=b

Headers:
Accept-Languange: chs
Host: example.com
...

[Body]
```

1. 请求方法（Method）：一个表示动作的单词，常用的有：`GET` `POST` `OPTIONS` `PUT` `DELETE`。
   用于告知服务器如何操作数据。
   例如，最常用的 `GET` 通常用于查询、获取的动作。
   `POST` 常用于修改、新建、提交等动作。

2. 通讯协议（Schema）：通常是 `http` 或者 `https`，现在以 `https` 为主流。
   `https` 跟 `http` 差不多，只是加了加密和一些安全规范。

3. 域名（Host）： 表示请求发向哪个服务器，域名最终会被解析成IP地址。

4. 路径（Path）： 通常表示使用服务器上的哪个接口。

5. 参数列表（Query）： 提供一些参数，通常跟在路径后面，以`?`开始，然后以`[key]=[value]`的形式，并用`&`符号分割。
   作用是用于提供处理接口业务逻辑必要的数据。

> 如果参数列表中，某一个参数需要用到诸如 `&` 符号之类的特殊符号作为字符串的值，怎么办？
> 例如： `key1=a&b`
>
> 可以参考阅读：[关于URL编码 - 阮一峰](https://www.ruanyifeng.com/blog/2010/02/url_encoding.html)


6. 标头（Header）： 你也可以看作是一种参数列表，但是这些列表中的元素，是对应于 `http` 请求的参数，而不是接口的参数。
   也就是说是用来描述 `http` 协议自身的一些属性。

7. 消息主体（Body）： 请求的主要数据，在 WebAPI 中，通常是以 UTF-8 编码的 JSON 字符串。
   需要注意的是：一般来说 `GET` `OPTIONS` `DELETE` 这几个常用的方法是不包含 `Body` 的。

> 标头 和 参数列表 的区别
>
> * 参数列表可以看作是调用一个函数的时候提供给函数的参数。
>   直接用于函数执行时，需要用到的数据。
>
> * 标头可以看作是全局变量、环境变量之类的提供给编译器的参数。
>
> 例如：有一个 API： `/add?a=1&b=2`
> 对于计算 `a+b` 来说， `a` 和 `b` 是直接用于调用 `/add` 接口的参数。
> 而我们在实现接口的时候，也可以读取一个 `Result-Base` 的标头，来确定返回的结果最终以几进制的形式。

## 服务器如何处理请求

请求发往哪个服务器，只根据域名的部分进行判定。 无论你使用什么方法、路径是怎样的、参数如何，只要域名的部分相同，那就最终会发往同一个服务器。

服务器在接受到来自客户端的请求后，通常会再根据路径，将请求发往对应的业务逻辑中。

## 服务器如何返回响应

通常来说，常见的服务器发回的响应是以 UTF-8 编码的字符串的形式。
但是严格来说是其实是以字节数据的形式存在的。
而且响应的标头会有一个 `Content-Type` 来表示响应内容的格式、类型。
也会有一个标头 `Encoding` 来表示使用什么编码进行编码的。

## 响应的组成

响应的组成跟请求差不多，但是有一些区别。

1. 没有请求方法。
2. 多了一个状态码，一般是三位数字，以`404`尤为有名。
   状态码中：
   * 一般 `2xx` 表示处理成功。
   * 一般 `4xx` 请求中有参数不合规或者未授权等由客户端造成的错误。
   * 一般 `5xx` 表示服务器处理过程中出现了异常。
3. 没有路径和参数等。
4. 主体可能有也可能为空。

## REST API

`REST` 是一种接口设计的规范。
详细可以参考：[表现层状态转换 - wiki](https://zh.wikipedia.org/wiki/%E8%A1%A8%E7%8E%B0%E5%B1%82%E7%8A%B6%E6%80%81%E8%BD%AC%E6%8D%A2)

### 要点

#### 1. 动词的选择

* GET（SELECT）：从服务器取出资源（一项或多项）。
* POST（CREATE）：在服务器新建一个资源。
* PUT（UPDATE）：在服务器更新资源（客户端提供完整资源数据）。
* PATCH（UPDATE）：在服务器更新资源（客户端提供需要修改的资源数据）。
* DELETE（DELETE）：从服务器删除资源。

#### 2. 路径的设计

设计路径的时候，通常使用复数名词并按照所属去划分层级。

例如：

`/users` 表示一个用户的资源。
`/users/permissions` 表示一个用户的权限。
`/users/favorites` 表示一个用户收藏的东西。

## 使用 gin 做个简单的接口服务器

### 初始化项目

```bash
go mod init gin_test
go get -u github.com/gin-gonic/gin
```

### 添加一个 GET /hello 接口

这一步我们添加一个 `GET /hello` 返回 `JSON` 格式的数据：

```json
{
  "msg": "Hello World"
}
```

1. 新建一个文件夹，叫作 `hello`，在里面新建一个 `hello.go`。
   内容如下：

```go
package hello

import "github.com/gin-gonic/gin"

func Hello(c *gin.Context) {
	c.JSON(200, gin.H{
		"msg": "Hello, World!",
	})
}
```

2. 在项目根目录下，创建一个 `main.go`，内容如下：

```go
package main

import (
	"gin_test/hello"

	"github.com/gin-gonic/gin"
)

func main() {
	r := gin.Default()

	r.GET("/hello", hello.Hello)

	r.Run()
}
```
