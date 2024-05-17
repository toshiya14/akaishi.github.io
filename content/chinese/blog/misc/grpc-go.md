---
title: "Go 语言简单 grpc 样例"
meta_title: ""
description: "本文是翻译整理自 grpc 官方文档的 quick start"
date: 2023-12-15T16:44:30+08:00
categories: ["杂七杂八"]
author: "Akaishi Toshiya"
tags: ["grpc", "golang"]
draft: false
---

## 安装 protobuf 编译器

```shell
go install google.golang.org/protobuf/cmd/protoc-gen-go@v1.28
go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@v1.2
```

> 如果后面步骤出现找不到 `protoc` 命令，则需要将 `GOPATH` 加到系统环境变量中。

## 定义 proto

创建一个文件，放置在 `helloworld/helloworld.proto`

grpc 使用 protobuf 来定义数据结构的，所以第一步，我们需要创建一个 `.proto` 文件。
在这个文件中定义我们需要的一些结构和 rpc 的方法。

```protobuf
// The greeting service definition.
service Greeter {
  // Sends a greeting
  rpc SayHello (HelloRequest) returns (HelloReply) {}
}

// The request message containing the user's name.
message HelloRequest {
  string name = 1;
}

// The response message containing the greetings
message HelloReply {
  string message = 1;
}
```

这里我们定义定义了一个 `Greeter` 的服务，这个服务包含一个 `SayHello` 方法。
`SayHello` 的请求为 `HelloRequest`，回复为 `HelloReply`。
并且这两个结构在下面也定义了具体的结构。

## 生成代码

进入项目根目录之后，执行下面的命令。

```shell
protoc --go_out=. --go_opt=paths=source_relative \
       --go-grpc_out=. --go-grpc_opt=paths=source_relative \
       helloworld/helloworld.proto
```

他会生成两个文件：

* `helloworld/helloworld.pb.go` - 用于对 `HelloRequest` 和 `HelloReply` 进行序列化等操作。
* `helloworld/helloworld_grpc.pb.go` - 生成的客户端和服务端的代码。

