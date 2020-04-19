---
title: Hexo + Github + Netlify 构建持续集成型静态博客
tags:
  - netlify
  - Hexo
  - github
categories:
  - [Hexo]
author: Rhysn
thumbnail: https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200410/netlifyblog/thumbnail.png
date: 2020-04-10 16:15:25
urlname: hexo_blog_github_host_netlify_cdn
---

## 概述

使用[Hexo][hexo] + [Github][github] + [Netlify][netlify]完成Blog搭建，实现仅通过更改Github中source文件夹内容（博客内容），Netlify自动完成Hexo的部署及发布，使得博客主仅关注于博客内容。

## 环境需求

### Node.js && Git

访问[Node.js][node.js_download]、[Git][git_download]下载并完成安装。

### 创建SSH Key && 配置Github

保证本地Git仓库与Github远程仓库的SSH加密传输顺利进行需要在本地环境中创建SSH Key，并在Github中添加公钥。

终端中执行如下命令，其中邮箱应替换成自己邮箱，之后采用默认设置即可（全部回车直到最后）。

```bash
ssh-keygen -t rsa -C "youremail@example.com"
```

![创建SSHKey](https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200410/netlifyblog/creadSSHKey.png)



访问用户目录下的 .ssh 文件夹，内部有私钥文件id_rsa、公钥文件id_rsa.pub，打开公钥文件复制其中全部内容在Github中添加此公钥。

![添加公钥](https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200410/netlifyblog/addSSHKey.png)

### 创建Github仓库

在[Github][github]中创建Blog仓库，本次使用公开仓，选用私有仓库在[netlify][netlify]设置中会有略微不同。

![创建GitHub仓库](https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200410/netlifyblog/createrepository.png)

### 安装Hexo

终端中执行如下命令完成Hexo的本地安装。

```bash
npm install -g hexo-cli
```

![安装Hexo](https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200410/netlifyblog/installHexo.png)

支持全部环境准备完毕。

## 本地Hexo博客

### 博客创建

终端中执行如下命令，完成Hexo的博客创建，其中folder为具体目录位置。

```bash
hexo init <folder>
```

![创建博客](https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200410/netlifyblog/hexoinit.png)

终端中进入folder文件夹，使用命令解决依赖关系。

```bash
cd <folder>
npm install
```

Ps：Windows需先进入相应盘在进入对应文件夹，以D盘为例。

```bash
d:
cd test/
npm install
```

![解决依赖关系](https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200410/netlifyblog/npminstall.png)

### 主题安装及配置

以[Suka][suka]为例，按照[文档教程][suka_doc]完成主题的安装及配置。

为保证主题能够被成功传输到[Github][github]中，需删除掉**主题文件夹**中的.git、.github两个文件夹及.gitignore文件。

### 查看Hexo效果

终端中执行如下命令：

```bash
hexo generate
hexo server
```

浏览器访问 http://localhost:4000 查看Hexo运行效果。

## Github远程仓

### 推送范围

访问项目根目录下.gitignore文件，没有直接新建填写如下内容，此部分设置Git忽略文件：

```properties
.DS_Store
Thumbs.db
db.json
*.log
public/
.deploy*/
```

由于需要解决主题中依赖关系，所以从默认设置中删除了node_modules，后续Netlify部分需要使用。

### 本地版本库

使用终端执行如下命令，创建本地版本库，folder为项目地址。

```bash
git init <folder>
```

添加Blog文件并提交事务，命令如下：

```bash
git add .
git commit -m "Hexo Blog"
```

### 推送Github

终端执行如下命令，完成向Github的推送，其中"git@github.com:username/repositoryname.git"部分为自己仓库地址。

```bash
git remote add origin git@github.com:username/repositoryname.git
git push -u origin master
```

完成推送，Github显示结果如图。

![仓库结果](https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200410/netlifyblog/githubrepo.png)

## Netlify持续集成

访问[Netlify][netlify]授权Github登录，并选择本次使用的Github仓库。

![仓库选择](https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200410/netlifyblog/choicerepository.png)

设置部署相关命令，按需设置。

![默认](https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200410/netlifyblog/buildsetting.png)

![clean](https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200410/netlifyblog/buildandclean.png)

等待netlify完成部署，显示如下。

![netlifypublished](https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200410/netlifyblog/netlifypublished.png)

访问netlify所生成的网址，显示Blog内容。

![blog](https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200410/netlifyblog/HexoBlog.png)

## 后续

之后仅需要变更source/_posts中的内容，即可完成blog内容的更新，Netlify将完全自动化完成Hexo的部署和发布，博主可以只关注文章的编写而无需关心后续的部署等操作。

同时netlify支持变更blog地址，甚至绑定域名，在此不做介绍。



[hexo]: https://hexo.io/
[github]: https://github.com
[netlify]: https://netlify.com
[markdown]: https://daringfireball.net/projects/markdown/
[node.js_download]: https://nodejs.org/en/download/
[git_download]: https://git-scm.com/download/
[suka]: https://theme-suka.skk.moe/
[suka_doc]: https://theme-suka.skk.moe/docs/
