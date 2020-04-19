---
title: 使用JSDELIVR加速GitHub
tags:
  - jsdelivr
  - cdn
  - github
categories:
  - [Tech,CDN]
author: Rhysn
thumbnail: https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200411/jsdelivr/thumbnail.jpg
date: 2020-04-11 17:15:25
urlname: jsdelivr_cdn_forgithub
---

## 概述

国内特殊的网络环境导致使用[Github][github]的资源速度并不美丽，但是在见招拆招的互联网江湖中，总是有一两个颇具侠客气质的剑客仗剑出手，所以今天要借助[JSDELIVR][jsdelivr]的免费CDN服务，完成[Github][github]的提速访问。

![logo](https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200411/jsdelivr/jsdelivr_logo.jpg)

## 使用方法

### 基础方法

使用 " cdn.jsdelivr.net/gh/用户名/仓库名@版本号/文件路径 " 访问Github中的资源，其中版本号可省略，此时使用最新版本。

例如：

```properties
// 原始路径
https://raw.githubusercontent.com/Rhysn/Ultranti/static/img/20200411/jsdelivr/jsdelivr_logo.png
// 对应 JSDELIVR 加速访问路径
https://cdn.jsdelivr.net/gh/Rhysn/Ultranti/static/img/20200411/jsdelivr/jsdelivr_logo.png
// 原始访问 jquery 3.2.1 路径
https://raw.githubusercontent.com/jquery/jquery/f71eeda0fac4ec1442e631e90ff0703a0fb4ac96/dist/jquery.min.js
// 对应 JSDELIVR 加速访问 jquery 3.2.1 路径
https://cdn.jsdelivr.net/gh/jquery/jquery@3.2.1/dist/jquery.min.js
// 省略版本号获取最新版本
https://cdn.jsdelivr.net/gh/jquery/jquery/dist/jquery.min.js
// 使用“.min”参数获取压缩版本文件
// 不存在时，JSDELIVR 将会自动生成一个并返回
https://cdn.jsdelivr.net/gh/jquery/jquery@3.2.1/src/core.min.js
// 获取文件夹中文件列表
https://cdn.jsdelivr.net/gh/jquery/jquery/
```

借助JSDELIVR的免费CDN服务将会极大的提高国内使用Github资源的速度，同时[JSDELIVR][jsdelivr]也提供了npm的加速服务，具体npm包可通过官网查询并匹配。

### 应用

借助[Github][github]、[JSDELIVR][jsdelivr]，可以搭建属于自己的图床、资源库等服务，其中版本号在Github中由Releases进行设置。

![releases](https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200411/jsdelivr/releases.jpg)

![releases_list](https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200411/jsdelivr/releaseslist.jpg)



[jsdelivr]: https://www.jsdelivr.com/
[github]: https://github.com
