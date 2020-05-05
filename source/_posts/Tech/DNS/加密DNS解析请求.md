---
title: 加密DNS解析请求
tags:
  - DNS
  - Surge
  - DoH
  - DoT
  - DNS JSON API
  - DNS Over HTTPS
  - DNS Over TLS
  - 公共 DNS
  - Public DNS
categories:
  - [Tech,DNS]
  - [Surge,DNS]
author: Rhysn
thumbnail: https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200503/dns/thumbnail.png
date: 2020-05-03 18:43:25
urlname: dns_safe
---

一直使用运营商默认分配的 DNS 服务，因为没有发现 DNS 劫持、投毒、准确性之类的问题，同时速度要远快于公共 DNS 服务，所以一直没有更换的想法，直到前几天发现一部分域名的 IP 被解析成了错误地址导致无法访问…

之后通过指定 `182.254.116.116 119.29.29.29 223.5.5.5 223.6.6.6` 腾讯和阿里运营的公共 DNS 进行测试，发现恢复正常，由此基本可以断定运营商所分配的 DNS 服务存在问题（指定运营商另一本地 DNS 测试正常）。

由于无法确定指定的运营商另一本地 DNS 后续不会出现问题，所以决定牺牲速度，选择使用公共 DNS 的方案。

其中阿里公共 DNS 服务现已支持 DoH、DoT，同时 Surge 已支持 DoH，所以最终方案就是路由器指定 DNS，Surge 配置 DoH，做次尝鲜。

## Base

由于 DNS 是默认跑在 TCP/53 端口或 UDP/53 端口上的服务，于是在域名解析请求上就存在一定的安全问题，与之对应的解决方案就是 DoH、DoT。

**DNS Over HTTPS(DoH)**
从名字上就是可以了解到，DoH 就是使用 HTTPS 协议完成解析请求的方案，从而保证用户解析请求不被监听和修改，本质就是一次 HTTPS 的请求过程，使用 TCP/443 端口。

**DNS Over TLS(DoT)**
与 DoH 对应，DoT 将直接使用 TLS 进行传输层加密来达到保护用户隐私安全的目的，默认使用 TCP/853 端口。Google 已在 Android 中原生支持了 DoT 的设置。具体设置步骤可以参考 AliDNS 帮助文档中 [DNS over TLS （DoT)][alidns_safe] 部分内容。

## AliDNS 安全传输服务

### DNS Over HTTPS(DoH)

```properties
// 域名形式 URL 接口
https://dns.alidns.com/dns-query?
// ip形式 URL 接口
https://223.5.5.5/dns-query?
https://223.6.6.6/dns-query?
https://2400:3200::1/dns-query?
https://2400:3200:b/dns-query?

```
参数仅有 `dns` 一个，具体内容为 `base64url编码的DNS请求报文`。返回内容与普通 DNS 解析请求返回报文一致。

### DNS JSON API

相对于 DoH ，DNS JSON API 提供了更友好的调用方式，无需再使用 base64url 进行编码，同时返回结果更易阅读，对于开发更加灵活。

```properties
//TLS 加密域名接口
https://dns.alidns.com/resolve?
//TLS 加密 IP 接口
https://223.5.5.5/resolve?
https://223.6.6.6/resolve?
https://2400:3200::1/resolve?
https://2400:3200:b/resolve?
//域名接口
http://dns.alidns.com/resolve?
//IP 接口
http://223.5.5.5/resolve?
http://223.6.6.6/resolve?
http://2400:3200::1/resolve?
http://2400:3200:b/resolve?

```

参数包含 `name、type、edns_client_subnet` 三项内容，分别对应域名、请求类型、用户子网信息。

其中请求类型分为 A 记录、CNAME、NS 记录等类型，具体可查阅阿里的帮助文档。

`edns_client_subnet` 将发送用户的子网信息，有助于加速目标网站的访问速度，通过返回与用户子网最近的 CDN 地址，减少链路深度实现。比如在香港的用户使用淘宝，将返回香港阿里云中的地址，而不是阿里云在杭州的地址。

返回结果为 JSON 类型，结构如下：

```json
{
    "Status": 0, 
    "TC": false, 
    "RD": true, 
    "RA": true, 
    "AD": false, 
    "CD": false, 
    "Question": {
        "name": "ultranti.com.", 
        "type": 1
    }, 
    "Answer": [
        {
            "name": "ultranti.com.", 
            "TTL": 300, 
            "type": 1, 
            "data": "104.28.9.92"
        }, 
        {
            "name": "ultranti.com.", 
            "TTL": 300, 
            "type": 1, 
            "data": "104.28.8.92"
        }
    ]
}
```

## Surge 中配置加密 DNS

本次配置使用 iOS Surge 4.2.1。

### 使用 DNS Over HTTPS(DoH)

```properties
# dns-server 形式
# 其中 `dns-server` 仅用于解析 `dns-server` 中的域名
[General]
dns-server = 182.254.116.116, 119.29.29.29, 223.5.5.5, 223.6.6.6
doh-server = https://dns.alidns.com/dns-query
doh-format = wireformat


# host形式
[General]
doh-server = https://dns.alidns.com/dns-query
doh-format = wireformat

[Host]
dns.alidns.com = server:223.5.5.5


# IP形式
[General]
doh-server = https://223.5.5.5.com/dns-query, https://223.6.6.6.com/dns-query
doh-format = wireformat
```

### 使用 DNS JSON API 

```properties
# dns-server 形式
# 其中 `dns-server` 仅用于解析 `dns-server` 中的域名
[General]
dns-server = 182.254.116.116, 119.29.29.29, 223.5.5.5, 223.6.6.6
doh-server = https://dns.alidns.com/resolve
doh-format = json


# host形式
[General]
doh-server = https://dns.alidns.com/resolve
doh-format = json

[Host]
dns.alidns.com = server:223.5.5.5


# IP形式
[General]
doh-server = https://223.5.5.5.com/resolve, https://223.6.6.6.com/resolve
doh-format = json
```

在实际使用时，使用域名形式请求返回报文更快，不知道是不是错觉。

## 其他 DoH 公共 DNS 服务

### 国内

|  运营方   | 调用 URL  |
| :------ | :------ |
| [红鱼](https://www.rubyfish.cn/dns/)  | https://dns.rubyfish.cn/dns-query |
| ...  | ... |

### 国外

|  运营方   | 调用 URL  |
| :------ | :------ |
| [Google](https://dns.google) | https://dns.google/dns-query |
| [AdGuard](https://dns.adguard.com/) | https://dns.adguard.com/dns-query |
| [Cloudflare](https://1.1.1.1) | https://cloudflare-dns.com/dns-query |
| [Cisco](https://opendns.com) | https://doh.opendns.com/dns-query |
| ...  | ... |


[alidns_safe]: https://www.alidns.com/faqs/#dns-safe
