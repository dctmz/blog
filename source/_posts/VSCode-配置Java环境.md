---
title: VSCode 配置Java环境
tags: [vscode, java, macOS, linux]
categories: [开发环境]
date: 2022-03-15 11:09:05
---

最近在学习 Java 开发，配置一套开发环境是必备的。由于我是做前端开发的，vscode 是比较喜欢的开发工具，而且他自带的远程开发功能非常好用，这里就讲下怎么用 vscode 搭建 Java 开发环境。由于我使用的是 Mac 系统，这里就只记录 Mac 系统的安装方式

<!-- more -->

## 必要条件

- 具有一定的程序开发经验
- 具有一定的 Linux 系统经验
- [homebrew](https://brew.sh/)

如有梯子最好，因为很多站点都是国外的

## vscode 安装

vscode 以一款开源的纯文本编辑器，具体安装方式，访问下面的 vscode 官网，直接下载对应的安装包即可

- https://code.visualstudio.com/

### vscode 插件

- [Extension Pack for Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)

## java 安装

由于一般情况下开发都采用 Java1.8 进行开发，这里是讲 1.8 的方法

```bash
brew install homebrew/cask-versions/adoptopenjdk8
```

配置环境变量

```bash
export JAVA_HOME="/Library/Java/JavaVirtualMachines/adoptopenjdk-8.jdk/Contents/Home/"
export PATH=$PATH:$JAVA_HOME/bin
```

测试

```bash
java -version

openjdk version "1.8.0_292"
OpenJDK Runtime Environment (AdoptOpenJDK)(build 1.8.0_292-b10)
OpenJDK 64-Bit Server VM (AdoptOpenJDK)(build 25.292-b10, mixed mode)
```

## 安装 MySQL

```bash
brew install mysql
```

## 安装 redis

```bash
brew install redis
```

以上安装完成基本的 Java 环境就完成了。

## 其他工具

- [tableplus 数据库连接工具](https://tableplus.com/)

## 拓展阅读

- [Mac brew 安装 Java8 && Mac 配置多个 Java 版本](https://blog.csdn.net/qq_44750620/article/details/116105197)
