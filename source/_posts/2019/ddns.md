---
title: 在家搭建网站-DDNS
date: 2019-04-14 18:48:35
tags: [建站笔记, 随便水水]

keywords: DDNS, CloudXNS, 个人网站搭建
comments: true
---

上次说了新买了一台小JJ，并且在上面搭建了个人网站，但是生命不息，折腾不止。

<!-- more -->

利用frp做内网穿透，确实是可以完成网站的搭建，但是frp的流量是要全部从服务器走的，速度受限，延时也比较高，所以一直想办法要改善一下，于是便想到了另一种解决方案——DDNS。

DDNS的先决条件是，你要有公网IP，如果没用公网IP的话，就别想了，乖乖的用内网穿透吧。

## DDNS

这次DDNS是使用了CloudXNS的API，其他DNS服务器应该也有类似的API，感兴趣的话可以自己去看一下。

DDNS脚本我是直接在GitHub上搜的 [https://github.com/kkkgo/CloudXNS-DDNS-with-BashShell](https://github.com/kkkgo/CloudXNS-DDNS-with-BashShell)，具体使用方法我简单示例一下。


首先是将域名DNS服务器改为CloudXNS（CloudXNS需要实名，介意请使用其他DNS服务器），具体教程可以看 [帮助](https://www.cloudxns.net/Support/detail/id/208.html)。

接入之后，我们先创建一条A记录解析，比如 `ddns.xyzabc.com`，然后再[API管理](https://www.cloudxns.net/AccountManage/apimanage.html)页面开启API，注意不用设置白名单。

我用的是宝塔面板，所以设置定时任务就非常简单了，把GitHub上的脚本复制到本地记事本里，然后改一下前面的变量，CHECKURL修改完了之后记得验证一下是否有效。本地搞完了，复制到宝塔的定时任务里，设置定时时间一分钟，然后立即执行一次，去CloudXNS检查一下，是不是IP已经解析过去了。

有了这条A记录之后，后面的就可以使用CNAME解析了。

## 端口映射

这个需要路由器支持，比如说我现在用的磊科的路由器，以前老板留下的。需要把外部的端口映射到NAT内网的某个IP上的某个端口

![端口映射](https://s2.ax1x.com/2019/04/29/E3bH9x.png)

这样，从外网的访问8080端口，就可访问到网站上的80端口。

## CDN

做完上面的就可以了，但是我为什么还要套一层CDN呢？因为我想要80端口和443端口。套CDN也很简单，和其他一样，回源地址填写你DNS解析A记录的域名，端口填写上面端口映射设置的外网域名就可以了，例如 `ddns.xyzabc.com:8083`，协议选择对应的协议。

目前我的小JJ上已经搭建了好多服务了，三个网站，一个webdav网站，视频转码，直播推流都放在了上面，小JJ总感觉不太够用，打算过几个月再换一台大JJ。

