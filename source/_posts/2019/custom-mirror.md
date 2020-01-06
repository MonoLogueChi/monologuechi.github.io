---
title: 腾讯云使用自定义镜像
date: 2019-10-02 22:15:35
tags: [建站笔记]

keywords: 腾讯云 自定义镜像 Debian10
comments: true
---

最近发现服务器上一些服务已经没用了，趁着十一假期整理一下，顺便研究一下腾讯云怎么使用自定义的镜像安装系统。


<!-- more -->

不得不吐槽一下，腾讯云的官方镜像实在是太少了，不像阿里那么丰富，对个人来说也许不怎么友好，但是对企业来说那就无所谓了。

## 工具

- 系统：win 10（家庭版不行）
- 虚拟机：Hyper-V
- Linux镜像文件（以Debian10为例）

## 文档

首先先看一下腾讯云的[官方文档](https://cloud.tencent.com/document/product/213/4942)。如果是不太熟悉虚拟化的朋友读下来，可能就是感觉云里雾里，不知道在讲什么。下面我就用一种很简单的方法去操作一边，演示怎么使用自定义的镜像。

## 创建快照

数据才是最重要的，使用自定义镜像装系统之前一定要创建快照。这一步你可以在后面制作完镜像以后再去做，但是重装系统之前一定要做。

![创建快照](/assets/img/2019/0.png)

## 基本要求

还是看文档，确定一下基本要求，要满足这些要求才可以正常制作镜像。

![镜像要求](/assets/img/2019/1.png)

排除操作系统不说，镜像格式需要注意一下，这次我打算选用VHD格式。  
文件系统，不能使用GPT分区，这个一定要注意，这是一个坑。

## 具体过程

### 创建虚拟机

![创建虚拟机](/assets/img/2019/3.png)

一定要注意，创建虚拟机要选择**第一代**，还记得前面提到的条件吗，虚拟硬盘要MBR分区表，不能使用GPT分区表。

### 安装系统

创建完虚拟机之后挂载已经下载好的镜像文件，由于Debian10镜像在第一代虚拟机里有问题，所以我用的Live版的Debian10镜像。  
附Debian10下载地址：[https://mirrors.cloud.tencent.com/debian-cd/](https://mirrors.cloud.tencent.com/debian-cd) （腾讯源，下载速度还可以）。

![安装系统](/assets/img/2019/4.png)

### 必要的配置

这一部分请详细阅读前面的官方文档，如果文档和本文有冲突，请以官方文档为准。

- 安装一些常用的工具，比如 `sudo`、`vim`等；
- 更新系统；
- 安装`cloud-init`，这一部分详见：[https://cloud.tencent.com/document/product/213/12587](https://cloud.tencent.com/document/product/213/12587)

这里重点说一下安装`cloud-init`，我选择的是自动安装方式：

```
sudo apt-get install cloud-init
```

Debian不需要修改`/etc/cloud/cloud.cfg`就可以直接使用，其他Linux没有尝试过。当然如果你愿意改那就改，比如说改一下镜像源等等，但是我相信如果你能知道怎么修改，就已经不需要看本文了。

后面几步操作我也不太明白具体是为了什么（仅Debian和Ubuntu需要，其他系统不清楚），反正按照官方要求删几个文件，改一个文件
```
cloud-init init --local
rm -rf /var/lib/cloud

rm -rf /etc/network/interfaces.d/50-cloud-init.cfg
```

修改`/etc/network/interfaces`为：
```
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).
source /etc/network/interfaces.d/*
```

也就是把后面的那部分删掉。

修改完之后直接关机。

### 转换虚拟硬盘格式

Hyper-V虚拟机的默认硬盘格式是VHDX，需要转换成VHD，Hyper-V虚拟机管理器本身就有这个功能，所以不需要再去额外安装其他的软件了。

如果有检查点的话，需要先删除检查点，右键点击然后选删除就可以了，不删除检查点是不能操作虚拟硬盘的。

在虚拟机设置里选中虚拟硬盘，转换之前先压缩一下（虽然什么用也没有，心理安慰）。

![转换格式](/assets/img/2019/5.png)

![转换格式](/assets/img/2019/6.png)

![转换格式](/assets/img/2019/7.png)

![转换格式](/assets/img/2019/8.png)

保存好导出的虚拟硬盘，等下需要上传这个东西。

### 导入自定义镜像

按照[官方文档](https://cloud.tencent.com/document/product/213/4945#.E5.AF.BC.E5.85.A5.E6.AD.A5.E9.AA.A4)给出的导入步骤，一部一部走下来就可以了。

记住上传的文件一定要有`公有读`权限。

![转换格式](/assets/img/2019/9.png)

我暂时不能确定是否是必须上传的`OOS`上，其他的链接没有尝试过，如果有大佬愿意尝试的话，可以试一下上传到其他服务器，看看分享出来的链接能不能用。

## 安装系统

这一步没啥说的，常规操作，记得一定要先创建快照，一定要先做好备份。

装完系统以后再修改一下软件源，改为腾讯云的好了，例如Debian10就修改为：

```
deb https://mirrors.cloud.tencent.com/debian buster main contrib non-free
deb https://mirrors.cloud.tencent.com/debian buster-updates main contrib non-free
deb https://mirrors.cloud.tencent.com/debian buster-backports main contrib non-free
deb https://mirrors.cloud.tencent.com/debian buster-proposed-updates main contrib non-free
#deb-src https://mirrors.cloud.tencent.com/debian buster main contrib non-free
#deb-src https://mirrors.cloud.tencent.com/debian buster-updates main contrib non-free
#deb-src https://mirrors.cloud.tencent.com/debian buster-backports main contrib non-free
#deb-src https://mirrors.cloud.tencent.com/debian buster-proposed-updates main contrib non-free

deb https://mirrors.cloud.tencent.com/debian-security buster/updates main
#deb-src https://mirrors.cloud.tencent.com/debian-security buster/updates main
```
