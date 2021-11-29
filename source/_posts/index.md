---
title: hexo 博客搭建
date: 2021-11-29 10:53:10
tag: [hexo, blog, github, pure]
---

本文介绍博客搭建的过程

## 初始化仓库

1. 申请 [GitHub](https://github.com/) 账号
2. 创建仓库

## 安装和配置主题

#### 安装博客

https://hexo.io/zh-cn/

使用以下命令安装博客

```bash
npm install hexo-cli -g
hexo init blog
cd blog
npm install
hexo server
```

#### 安装主题

我这里使用的是 [pure](https://github.com/cofess/hexo-theme-pure)

在博客目录下面运行

```bash
git clone https://github.com/cofess/hexo-theme-pure.git themes/pure
```

#### 配置主题

1. 在项目目录下面新建`_config.pure.yml`
2. 复制`themes/pure/_config.yml`文件的内容到`_config.pure.yml`
3. 配置你的内容

## 部署到 GitHub

#### 安装插件

在项目根目录下运行以下命令

```bash
npm install hexo-deployer-git --save
```

#### 配置插件

在项目根目录下的`_config.yml`找到`deploy`配置项

```yml
deploy:
  type: git
  repo: <repository url> # https://bitbucket.org/JohnSmith/johnsmith.bitbucket.io
  branch: [branch]
  message: [message]
```

配置好后如下

```yml
deploy:
  type: git
  repo: git@github.com:dctmz/blog.git #https://bitbucket.org/JohnSmith/johnsmith.bitbucket.io
  branch: gh-pages
```

#### 运行发布命令

```bash
hexo clean && hexo deploy
```

#### GitHub设置

一般情况下，GitHub已经自动设置好了，如果没有设置好，找到[page](https://github.com/dctmz/blog/settings/pages)设置

我的设置如下

![](https://gitee.com/dctxf/pic/raw/master/2021-11-29-16 15:27: 340: 6i8nmy8ko2:1638173727337.png)

#### 预览

接下来就可以访问你的博客了，如我的网址为

[https://dctmz.github.io/blog/](https://dctmz.github.io/blog/)

改为你的地址即可

```html
https://[username].github.io/[repo]/
```

