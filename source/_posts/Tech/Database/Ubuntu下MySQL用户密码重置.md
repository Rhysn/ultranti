---
title: Ubuntu下MySQL用户密码重置
tags:
  - Ubuntu
  - MySQL
  - Database
categories:
  - [Linux,Ubuntu]
  - [Database,MySQL]
author: Rhysn
thumbnail: https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200912/mysql_password/user_table1.png
date: 2020-09-12 21:49:36
urlname: setting_mysql_password_on_Ubuntu
---

今天在云服务器上的 Ubuntu 下安装了 MySQL，结果遭遇了无法使用 root 用户登陆的窘境，也便有了一番折腾，记录下具体步骤。

### 整体环境

OS：Ubuntu Server 20.04 LTS 64位

Database：MySQL 8.0.21

### 问题

安装 MySQL 后使用 root 账户进行登录，提示 ERROR 1698（28000）…

![ERROR1698](https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200912/mysql_password/error1698.png)

### 获取 debian-sys-maint 账户密码

debian-sys-maint 账户是 Debian、Ubuntu 下安装 MySQL 默认存在一个账户，而我们可以使用此账户登陆 MySQL 再对 root 用户密码进行重置。

查看 debian.cnf 文件获取密码。

```shell
sudo cat /etc/mysql/debian.cnf
```
结果中 password 部分就是需要的密码。

![password](https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200912/mysql_password/debian_password.png)

### 修改 MySQL root 密码

1. 使用 debian-sys-maint 用户登陆 MySQL

![login mysql](https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200912/mysql_password/debian_mysql.png)

2. 查看 MySQL 的用户表

先切换数据库，在执行查询。

```mysql
use mysql;
select host,user,plugin,authentication_string from user;
```

![用户表](https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200912/mysql_password/user_table1.png)

3. 设置 'root' 密码

```mysql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
```

![设置密码](https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200912/mysql_password/setting_password.png)

4. 再次查看用户表内容

```mysql
select host,user,plugin,authentication_string from user;
flush privileges;
```

![用户表](https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200912/mysql_password/user_table2.png)

### 使用 root 用户登录 MySQL

```mysql
mysql -u root -p
```

![登录MySQL](https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200912/mysql_password/mysql_root.png)