---
title: Voicemeeter 界面介绍
date: 2020-06-09 12:58:35
tags: ["涨姿势"]

keywords: Voicemeeter 使用教程 虚拟麦克风 虚拟播放器
comments: true
---

最近在帮忙做直播，用到了 Voicemeeter，就来简单的讲一下这个东西应该怎么用，顺便举例子说几个应用场景。

<!-- more -->

本文讲解用到的版本是 banana，版本号为 2.0.5.3，下载地址为 [https://www.vb-audio.com/Voicemeeter/banana.htm](https://www.vb-audio.com/Voicemeeter/banana.htm)，官方文档 [https://www.vb-audio.com/Voicemeeter/banana.htm#Documentation](https://www.vb-audio.com/Voicemeeter/banana.htm#Documentation)。

## 界面

banana 版本拥有 3 个物理输入，2 个虚拟输入，3 个无论输出，2 个虚拟输出，共 5 个输入和 5 个输出。

![](/assets/img/2020/Snipaste_2020-06-09_14-54-10.png)

输入输出口的对应关系分别为：

![](/assets/img/2020/Snipaste_2020-06-09_14-43-34.png)

界面上主要就是这个东西，那个磁带标志是一个录音机，剩下的 EQ 之类的，作为进阶技巧，想要学习的话可以自己去看官方的文档。关于压缩门和噪声门，可以自行查找调音相关知识。

## 使用示例

### 解决炸麦

输入硬件(1)选择你的麦克风，输出硬件(A1)选择你的耳机，这个时候你说话是可以在耳机里听到的。

先调高噪音门，调到你不说话的时候完全静音，正常说话的时候可以正常收音。然后适当调高压缩器，不能解决炸麦的话再适当降低增益。

解决之后在输入那里取消 A1 的勾选，只留下 B1，将 VoiceMeeter Output 设为默认设备，或者在需要使用的软件里选择 VoiceMeeter Output 作为麦克风。

### 声音分流

将声音分给多个设备输出，多机直播的时候会用得到。

将电脑的声音默认输出设备选择为 VoiceMeeter Input，也就是一个虚拟输入，然后将这一路输入同时分配给 A1 和 A2，这样就可以同时在 A1 和 A2 输出同样的声音了。

### 虚拟音频通道

有的时候需要单独捕获某个程序的声音，这个时候可以将其单独输出到电脑某个硬件上，然后去捕获这个有硬件的声音，但是有的电脑只有一个声卡，也就是只有一个输出设备，我们就可以用 VoiceMeeter 组建一个虚拟通道了。

软件中声音输出选择 VoiceMeeter Aux Input（不被占用的那个），被选择的虚拟输入那里直接输出到 B2，然后在采集软件那里监听 VoiceMeeter Aux Output。

### 混音

很简单，就是把需要的输入输出到同一个输出里面，然后调节相关参数就可以了。