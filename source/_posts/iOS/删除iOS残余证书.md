---
title: 删除iOS残余证书
tags:
  - iOS
  - certificate
  - SQLite
  - iMazing
  - Windows
  - DB Browser for SQLite
categories:
  - [iOS]
  - [Tools,DB Browser for SQLite]
author: Rhysn
thumbnail: https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200420/certificate/thumbnail.png
date: 2020-04-20 16:16:29
urlname: ios_delete_certificate_on_windows
---

iCloud 是个神奇的东西，给予 iOS 便捷云备份的同时，也存在证书描述文件不备份却单独备份证书的情况。

在 iOS 中使用 Surge、Thor 等抓包 App 难免会生成并安装 SSL 证书用以解密 HTTPS，从而导致了 iCloud 在新手机恢复旧机备份时， `设置 > 通用 > 关于本机 > 证书信任设置` 中存在未删除的旧证书，同时由于缺少对应的描述文件，导致无法删除、无法使用的尴尬。

证书信任设置：
![证书信任设置](https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200420/certificate/certificate.jpg)

描述文件：
![描述文件](https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200420/certificate/profiles.jpg)

## macOS 解决方案(推荐，快、易)

一顿找寻之后就指向了这样一个[解决方案][answer]，整体思路就是从备份文件中导出存储证书的 `sqlite3` 文件，使用脚本恢复对应的 `crt` 证书文件（可能是多个），之后重新安装证书，恢复描述文件并覆盖证书，最后删除不再使用的证书描述文件 `设置 > 通用 > 描述文件` ，完成残余证书的删除操作，抄录教程如下：

> 1. Backup iPhone to Mac, View backup file by some software (I used iMazing)
>
> 2. Find TrustStore.sqlite3 in Backup/KeychainDomain/ and export it to HOME DIR.
> 
> 3. Use this project https://github.com/ADVTOOLS/ADVTrustStore to export certfile
> 
>   ```bash
>    ./iosCertTrustManager.py -t ~/TrustStore.sqlite3 -e ~/foo.crt
>   ```
> 
>4. Airdrop or Email this crt file to iOS device, and install it.
> 
> 5. Find it in Settings > General > Profiles and Remove it.
> 
>6. It disappear in "Certificate Trust Settings"

在第三步中使用 GitHub 中的开源项目 [ADVTrustStore][advtruststore] 完成证书的导出，在 Windows 下并不能顺利执行，仅支持 macOS 下运行，。

## Windows 解决方案

既然已经确认 `TrustStore.sqlite3` 就是存储信任证书的备份文件，那么只要在Windows下打开 `sqlite3` 文件并导出相应证书，重装再删除即可。

在 Windows 下可通过安装 [DB Browser(SQLite)][dbbrowser] 打开并导出对应证书。

![证书导出](https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200420/certificate/sqlite3.jpg)

注意，图中每一行为一张证书的信息，有几行就需要导出几次，导出文件的后缀需要自行修改成 `.crt` 。

之后就只需要按照步骤再安装删除即可。

[dbbrowser]: https://sqlitebrowser.org

[ answer ]: https://apple.stackexchange.com/questions/300203/how-can-i-delete-a-certificate-that-got-restored-from-a-backup-under-ios-10-11

[advtruststore]: https://github.com/ADVTOOLS/ADVTrustStore