---
title: "如何自签名证书"
meta_title: ""
description: "本文讲述了如何自签名 CA 证书和客户端证书"
date: 2023-12-13T02:24:30+08:00
categories: ["杂七杂八"]
author: "Akaishi Toshiya"
tags: ["SSL", "openssl"]
draft: false
---

本文主要讲述了如何签名 CA 证书，以及使用 CA 证书签名客户端证书并生成证书链。
基本使用 `openssl`。

## 生成 CA 证书

### 1. 生成 CA 证书使用的密钥文件

```bash
openssl genrsa -out ca.key 4096
```

最后的 `4096` 参数为密钥的长度，越长越安全。

### 2. 由密钥生成 crt 证书

```bash
openssl req -x509 -sha256 -days 36500 -nodes -key ca.key -subj '/CN=*.example.com/C=CN/L=Beijing' -out root.crt
```

* `-days 36500` 为证书有效天数，这里使用了 100年。
* `-key ca.key` 是上一步我们生成的密钥文件。
* `-subj` 后面的参数：
  * `CN=` 签发域名名称，也可以是主机名称，不一定要域名。
  * `C=` 为两位国家代码。
  * `L=` 为 `Locality`，地区。
  * 也可以指定其他 `x509` 规范中的字段。

### 3. 转换为 pfx 格式证书

一般来说，如果支持使用 `crt` + `key` 组合的地方，上面的步骤完成之后，就已经可以使用了。

如果有特别的需求，需要使用 `pfx`（一般包含密钥）的话，可以通过下面指令来转换。

```bash
openssl pkcs12 -export -inkey ca.key -in ca.crt -out ca.pfx
```

执行之后，会提示你输入一个导出密码，如果你输入了密码，那么每次用到该证书的时候，都要配套使用你在这里设置的密码。
你也可以直接按两次回车，不设置密码。

## 生成客户端证书

### 1. 生成密钥

和上面一样。

```bash
openssl genrsa -out client.key 4096
```

### 2. 生成 csr 证书

```bash
openssl req -new -key client.key -out client.csr -sha256 -subj '/CN=Client Name'
```

### 3. 签名，并生成 crt 证书

首先我们需要新建一个 `client.cnf` 的文件，输入下面内容。

```ini
[client]
basicConstraints = CA:FALSE
nsCertType = client, email
nsComment = "Local Test Client Certificate"
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid,issuer
keyUsage = critical, nonRepudiation, digitalSignature, keyEncipherment
extendedKeyUsage = clientAuth, emailProtection
```

需要注意的是，上面的 `nsComment` 请修改为证书标识名称，其他可不变。

如果我们不需要客户端证书，而是需要一个用于 `ssl` 的证书，那么可以使用下面的 `cnf`：

```ini
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
subjectAltName=@alt_names

[alt_names]
DNS.1=your.domain.name
```

唯一比较重要的是 `DNS.1` 必须与你的服务器域名一致。

然后运行：

```bash
openssl x509 -req -days 36500 -in client.csr -sha256 -CA ca.crt -CAkey ca.key -CAcreateserial -out client.crt -extfile client.cnf -extensions client
```

### 4. 生成证书链

```bash
cat client.key client.crt ca.crt > client.pem
```

如果使用的 Windows，可以使用以下 PowerShell 指令。

```powershell
Get-Content client.key, client.crt, ca.crt | Set-Content client.pem
```

### 5. 转换成 pfx 证书

```bash
openssl pkcs12 -export -in client.pem -inkey client.key -certfile ca.crt -out client.pfx
```

执行之后，会提示你输入一个导出密码，如果你输入了密码，那么每次用到该证书的时候，都要配套使用你在这里设置的密码。
你也可以直接按两次回车，不设置密码。
