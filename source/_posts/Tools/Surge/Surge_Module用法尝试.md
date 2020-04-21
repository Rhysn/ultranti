---
title: Surge Module 用法尝试
tags:
  - Surge
  - script
  - testflight
categories:
  - [tools,Surge]
author: Rhysn
thumbnail: https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200414/SurgeModule/thumbnail.jpg
date: 2020-04-14 16:56:29
urlname: surge_testflight_module
---

Surge 在最新的 iOS TF 版本中加入了 [Module][surge_module] 的新玩法，具体介绍如下：

> Module（模块）是一系列设置的集合，可用于覆盖当前配置的部分设定，有非常多的使用场景：
>
> - 微调不可编辑的配置的设定，如托管配置和企业配置。
> - 快捷的在不同工作环境中切换，比如临时开启对所有域名的 MitM 并调整过滤器。
> - 使用他人编写的模块以完成某些特定任务，比如，你的同事可以编写一个模块将应用的 API 请求重定向至测试服务器。
> - 如果你在多个设备间使用了同一份配置，有可能需要根据设备的使用场景进行微调。模块的开启状态是保存于当前设备的，可以用于在不同设备间的差异性修改。
>
> ### 基本概念
>
> 模块相当于给当前配置进行 Patch，其优先级高于配置本身的设置。有三种模块：
>
> - 内置模块：Surge 会预置一些模块，随着 Surge 自身更新。
> - 本地模块：放置配置文件根目录的 .sgmodule 文件
> - 安装的模块：从某个 URL 安装的模块
>
> 你可以同时开启多个模块，模块的开启状态保存于当前设备，不会进行同步。切换配置也不影响模块的开启状态。
>
> ### 编写模块
>
> 模块的内容和标准配置基本一致，目前支持调整以下段：
>
> - General，Replica
>
>   有三种写法
>
>   - key = value：直接覆盖原始值
>   - key = %APPEND% value：在原始值的末尾进行追加（仅适用于适用逗号分隔的字段）
>   - key = %INSERT% value：在原始值的开始进行插入（仅适用于适用逗号分隔的字段）
>
> - MITM
>   仅支持操作 hostname 字段，同样支持上述三种写法。
>
> - Script，URL Rewrite，Header Rewrite，Host
>   新加入的定义将会追加在原始内容的顶部。
>
> - Rule
>
>   - 新配置的规则将被插入在最顶部
>   - 规则只可以使用 DIRECT、REJECT、REJECT-TINYGIF 三个策略
>
> 同时，模块支持配置 name，desc 和 system 描述，请参照最后的样例。
> (system 描述的可取值为 ios 和 mac，用于限制模块的使用范围)

根据 Module 的设定，我尝试编写了 MITI && Script 的 Cookies 获取部分内容，同时引入 jsDelivr CDN 加速链接，实现只在必要时刻启用 MITI 中间人攻击，减少长时间开启非必要 MITI 解密，或者反复修改配置文件的繁琐。

```properties
#!name=Get Cookies With Check In
#!desc=MITM && Script

[MITM]
hostname = %APPEND% api.m.jd.com, *.iqiyi.com, icbc1.wlphp.com:8444, api-hdcj.9w9.com, mobwsa.ximalaya.com, wapside.189.cn:9001, daojia.jd.com, gameapi.hellobike.com
[Script]
jddaily_getcookie = type=http-request,pattern=https:\/\/api\.m\.jd\.com\/client\.action.*functionId=signBean,script-path=https://cdn.jsdelivr.net/gh/NobyDa/Script@master/JD-DailyBonus/JD_DailyBonus.js
iqiyi_getcookie = type=http-request,pattern=https?:\/\/.*\.iqiyi\.com\/.*authcookie=,script-path=https://cdn.jsdelivr.net/gh/NobyDa/Script@master/Surge/iQIYI-DailyBonus/iQIYI_GetCookie.js
icbc_getcookie = type=http-request,pattern=^https:\/\/icbc1\.wlphp\.com:8444\/js\/api\/index\/signIn,script-path=https://cdn.jsdelivr.net/gh/Rhysn/Asu@master/Scripts/ICBC/icbc_cookies.js
wechatlottery_getcookie = type=http-request,pattern=^https:\/\/api-hdcj\.9w9\.com\/v\d+\/sign,script-path=https://cdn.jsdelivr.net/gh/zZPiglet/Task@master/WeChatLottery/WeChatLottery_new.js
ximalaya_getcookie = type=http-request,pattern=^https?:\/\/.*\/mobile\-user\/homePage\/.*,script-path=https://cdn.jsdelivr.net/gh/chavyleung/scripts@master/ximalaya/ximalaya.cookie.js
10k_getcookis = type=http-request,pattern=^https:\/\/wapside.189.cn:9001\/api\/home\/homeInfo,script-path=https://cdn.jsdelivr.net/gh/chavyleung/scripts@master/10000/10000.cookie.js
10k_getcookie_response = type=http-response,pattern=^https:\/\/wapside.189.cn:9001\/api\/home\/homeInfo,script-path=https://cdn.jsdelivr.net/gh/chavyleung/scripts@master/10000/10000.cookie.js
jddj_getcookie = type=http-request,pattern=^https:\/\/daojia.jd.com/client(.*?)functionId=signin(.*?)userSigninNew,script-path=https://cdn.jsdelivr.net/gh/chavyleung/scripts@master/jddj/jddj.cookie.js
hellobike_getcookie = type=http-request,pattern=^https:\/\/gameapi\.hellobike\.com\/api,script-path=https://cdn.jsdelivr.net/gh/chavyleung/scripts@master/hellobike/hellobike.js

```

Ps：
具体 Surge 脚本实现全部来源于网络，可在对应 GitHub 仓库中查看，相关介绍可阅读对应 README 文档。

[surge_module]: https://community.nssurge.com/d/225-module