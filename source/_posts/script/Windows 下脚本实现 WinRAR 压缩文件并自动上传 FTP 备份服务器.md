---

title: Windows 下脚本实现 WinRAR 压缩文件并自动上传 FTP 备份服务器 
tags:
  - Database
  - FTP
  - script
  - Windows
categories:
  - [脚本]
author: Rhysn
thumbnail: https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200617/windows_database_backup_ftp_script/thumbnail.jpg
date: 2020-06-17 15:36:04
urlname: windows_database_backup_ftp_script
---

单位做了两年多的异地容灾项目依然未能成功落地，结果遭遇了数据库备份服务器的故障退役，临时由应用服务器代替，目前的状态就是每天通过应用服务器完成数据库的全局导出，备份在本地，每隔一段时间人为清理一遍旧备份保证应用服务器的磁盘容量。

基于现状，可以通过脚本压缩每天的备份文件，再将压缩文件传输到特定 PC 机（成本低廉）上，删除应用服务器上的备份文件来减少空间的占用。

## 备份压缩

整体服务器都是跑在 Windows Server 之上的，所以备份压缩就选用了 WinRAR 实现。备份文件包含 `dmp`、`log`、`lts` 三个文件，文件命名规则为 `DAYODB` 前缀加当日日期。

```shell
rem 指定数据库备份文件所在目录
set DIRECTIONPATH=D:\database

rem 设置备份文件前缀
set FILENAME=DAYODB

rem 拼凑当日日期，例如：20200617
set TIMESTYLE=%date:~0,4%%date:~5,2%%date:~8,2%

rem 指定当日的三个备份文件地址，拼凑dmp、log、lts三个文件完整地址
set FILEPATH=%DIRECTIONPATH%\%FILENAME%%TIMESTYLE%.dmp
set LOGPATH=%DIRECTIONPATH%\%FILENAME%%TIMESTYLE%.log
set LSTPATH=%DIRECTIONPATH%\%FILENAME%%TIMESTYLE%.lst

rem 指定压缩文件地址
set RARPATH=%DIRECTIONPATH%\%FILENAME%%TIMESTYLE%.rar
```

使用 WinRAR 压缩文件

```shell
rem 指定WinRAR地址
set RAR_CMD="C:\Program Files\WinRAR\WinRAR.exe"

rem 使用WinRAR压缩备份文件
%RAR_CMD% a -df "%RARPATH%" "%FILEPATH%" "%LOGPATH%" "%LSTPATH%"
```

## FTP 文件传输

创建 FTP 传输命令文件databaseftp.txt，默认保存在当前目录下。

```shell
rem 打开192.168.1.1:33,根据实际FTP配置进行设置
(echo open 192.168.1.1 33
rem 指定FTP用户名及密码
echo administrator
echo 159
rem 上传目标文件，%RARPATH%由前一部分压缩部分指定
echo put %RARPATH%
echo bye)>databaseftp.txt
```

调用生成的命令文件，完成文件上传，`databaseftp.txt` 为上面写入的文件。

```shell
ftp -i -s:databaseftp.txt
```

## 删除本地备份文件

```shell
del "%RARPATH%"
```

## 完整脚本

```shell
@echo off

set RAR_CMD="C:\Program Files\WinRAR\WinRAR.exe"

set DIRECTIONPATH=D:\database

set FILENAME=DAYODB

set TIMESTYLE=%date:~0,4%%date:~5,2%%date:~8,2%

set FILEPATH=%DIRECTIONPATH%\%FILENAME%%TIMESTYLE%.dmp

set LOGPATH=%DIRECTIONPATH%\%FILENAME%%TIMESTYLE%.log

set LSTPATH=%DIRECTIONPATH%\%FILENAME%%TIMESTYLE%.lst

set RARPATH=%DIRECTIONPATH%\%FILENAME%%TIMESTYLE%.rar

echo 压缩备份文件，请稍等......

echo ============================================

%RAR_CMD% a -df "%RARPATH%" "%FILEPATH%" "%LOGPATH%" "%LSTPATH%"

echo 压缩完成！

(echo open 192.168.110.22 33523
echo administrator
echo 159
echo put %RARPATH%
echo bye)>databaseftp.txt

echo 异地传输
ftp -i -s:databaseftp.txt
del databaseftp.txt

echo 上传完成，删除本地文件
del "%RARPATH%"

```

