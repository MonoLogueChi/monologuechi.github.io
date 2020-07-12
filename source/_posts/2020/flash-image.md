---
title: 软路由烧写镜像
date: 2020-07-12 18:05:30
tags: ["软路由"]

keywords: 软路由 烧写镜像 U盘
comments: true
---

简单说一下怎么给软路由装系统

<!-- more -->

## 下载镜像

首先需要下载一个合适的镜像，openwrt 的话可以在 GitHub 上找，也可以在恩山去找别人编译的，也可以自己动手编译一个，不想找的话可以用我自己编译的镜像

[下载地址](https://dl.u2sb.top/#/s/GPIQ?path=%2Fblog%2F%E8%BD%AF%E8%B7%AF%E7%94%B1%2FOpenWRT)

里面每个镜像都不一样，直接往 U 盘里烧，选择 img 格式的即可。

## 准备软件

推荐使用的软件是 [etcher](https://etcher.io/) 或者 [rufus](https://rufus.ie/)

这两个软件都挺实用的，下面我以 etcher 为例，简单说明一下怎么烧录镜像。

## 烧录镜像

插上 U 盘，然后打开软件。

![烧录镜像](/assets/img/2020/Snipaste_2020-07-12_18-25-54.png)

拖入待烧录的文件，选择 U 盘设备，然后点击 Flash 即可。

等进度条走完了，弹出 U 盘，拔掉就可以了。
