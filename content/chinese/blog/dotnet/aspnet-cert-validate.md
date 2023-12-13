---
title: "在 ASP.NET 中使用自签名证书进行客户端证书验证"
meta_title: ""
description: "本文讲述了如何使用自签名证书在 ASP.NET Web 项目中进行客户端证书的验证。"
categories: ["dotnet", "ASP.NET"]
author: "Akaishi Toshiya"
date: 2023-12-13T03:09:06+08:00
tags: [".NET", "ASP.NET", "SSL", "客户端证书"]
draft: false
---

## 准备工作

可以参考 [如何创建自签名证书]({{< ref "/blog/misc/self-signed-cert.md" >}})

我们需要使用到：
* `ca.pfx` 根证书
* `client.pfx` 客户端证书

如果有多个客户端，可以参考生成 `client.pfx` 的生成方式为不同客户端生成不同的证书。
（虽然使用同一个证书也不是不可以，但是终归没有那么安全。

## 基本思路

这里我们采用的方法，是在 `Kestrel` 中允许客户端是使用任意证书进行连接，而在受保护的 API 中加入一个 `ActionFilter` 而对部分 API 进行保护。

如果需要对所有的 API 进行保护，也可以将验证证书的逻辑移动至 `AddCertificate` 配置中。

参考：[在 ASP.NET Core 中配置证书身份验证](https://learn.microsoft.com/zh-cn/aspnet/core/security/authentication/certauth)

## 配置 Kestrel

在 `Program.cs` 中加入下面代码以接受所有客户端证书。（`builder.Build`之前）

```C#
builder.WebHost.ConfigureKestrel(c =>
{
    c.ConfigureHttpsDefaults(
    https =>
    {
        https.ClientCertificateMode = Microsoft.AspNetCore.Server.Kestrel.Https.ClientCertificateMode.AllowCertificate;
        https.AllowAnyClientCertificate();
    });
});
```

## 实现 ActionFilter

这里我们新建一个类，继承自 `ActionFilterAttribute`。

```C#
public class ValidateClientCertAttribute : ActionFilterAttribute
{
    private static readonly X509Certificate2 ca;
    static ValidateClientCertAttribute() {
        ca = new X509Certificate2(Path.Combine(AppDomain.CurrentDomain.BaseDirectory, "ca.pfx"), "Password");
    }

    static bool ValidateCert(X509Certificate2 cert) {
        var chain = new X509Chain();
        chain.ChainPolicy.RevocationMode = X509RevocationMode.NoCheck;
        chain.ChainPolicy.RevocationFlag = X509RevocationFlag.ExcludeRoot;
        chain.ChainPolicy.VerificationFlags = X509VerificationFlags.AllowUnknownCertificateAuthority;
        chain.ChainPolicy.VerificationTime = DateTime.Now;
        chain.ChainPolicy.UrlRetrievalTimeout = new TimeSpan(0, 0, 0);
        chain.ChainPolicy.ExtraStore.Add(ca);
        return chain.Build(cert);
    }

    public override async Task OnActionExecutionAsync(ActionExecutingContext context, ActionExecutionDelegate next)
    {
        // Verify cert.
        var req = context.HttpContext.Request;
        if (!req.IsHttps)
        {
            throw new HttpRequestException("This endpoint only supports HTTPS request.", null, System.Net.HttpStatusCode.BadRequest);
        }

        var cert = await context.HttpContext.Connection.GetClientCertificateAsync() ?? throw new HttpRequestException("Unauthorized client.", null, System.Net.HttpStatusCode.Unauthorized);

        if (!ValidateCert(cert)) {
            throw new HttpRequestException("Failed to validate the certificate.", null, System.Net.HttpStatusCode.Unauthorized);
        }

        await next();
    }
}
```

* `"Password"` 修改为生成 `ca.pfx` 证书时输入的导出密码，如果没有设置导出密码，这个参数整个删掉。

添加了该 `ActionFilter` 之后的接口：

* 如果客户端请求该接口，并且没有带任何证书，则会提示 `Unauthorized client.`。
* 如果带的客户端证书不是由 `ca.pfx` 签名的证书，则会提示 `Failed to validate the certificate.`。

## 将 ca.pfx 添加到生成

将 `ca.pfx` 文件添加到项目根目录，并且设置 **复制到输出目录** 为 **始终复制**。

## 保护需要的 API

在需要保护的 API 上，加入 `[ValidateClientCert]` 即可。

```C#
[ValidateClientCert]
[HttpGet("cert")]
public ActionResult<string> ValidateCert()
{
    return this.Ok("OK");
}
```

## 客户端的写法

如果你的客户端也是使用 **.NET 5.0以上** 进行开发的，可以参考下面的代码：

```C#
var handler = new HttpClientHandler
{
    ClientCertificateOptions = ClientCertificateOption.Manual,
    SslProtocols = SslProtocols.Tls12,
};
var cert = new X509Certificate2(@"client.pfx", "Password");
handler.ClientCertificates.Add(cert);
var http = new HttpClient(handler);

// Request
var resp = this.http.GetAsync("https://localhost:7011/api/tests/cert").Result;
if (resp.IsSuccessStatusCode)
{
    var content = resp.Content.ReadAsStringAsync().Result;
    Console.WriteLine(content);
}
else
{
    Assert.Fail($"{resp.StatusCode}: {resp.Content.ReadAsStringAsync().Result}");
}
```

对应的，这里的 `password` 也需要修改为之前生成证书时，生成 `client.pfx` 时输入的导出密码。
