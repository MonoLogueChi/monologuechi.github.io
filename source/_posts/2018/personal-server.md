---
title: 二手洋垃圾改造
date: 2018-09-22 20:40:04
tags: [服务器, 个人电脑]

keywords: 服务器 二手 洋垃圾 个人电脑 水冷
comments: true
---

最近一直我那台老掉牙的笔记本实在是不行了，看现在电脑实在是太贵了，就搞了台二手洋垃圾回来，然后自己动手魔改一番。

<!-- more -->

> 现在配件还没到齐，还差一个调速器没到，所以暂时就用我的可调电源代替了。

先看一下整体配置：

|   名称  |   价格  |   备注  |
|:-------:|:-------:|:-------|
|主机|1990|二手戴尔R710，双路E5645，32G DDR3内存，1T SAS硬盘|
|显示器|880|飞利浦243S7EHMB|
|固态硬盘|330|台电A850，垃圾货一个，千万别买|
|显卡|270|二手750ti|
|水冷套件|300|某宝买的，只要不漏水就好|
|线材配件|约100|电源线，VGA线，硬盘线，供电转接线，PWM控制器，螺丝等配件|
|总计|约3900| 还顺便买了一个鼠标，还有电烙铁和一捆杜邦线，全加起来应该有4000了 |

## 显示器

先来说显示器，飞利浦243S7EHMB这款显示器只能说是中规中矩，千元以内还算不错，旋升支架算是一个优点。

![243S7EHMB](https://s1.ax1x.com/2018/09/22/iuMpFA.jpg)

色彩一般般，反正肉眼看是足够了，如果不是缺钱的话，不建议入手，建议加500块上个更好的。

## 显卡

伊拉克橙色，二手750ti一张

![显卡](https://s1.ax1x.com/2018/09/22/iuKzod.jpg)

## 主机

戴尔R710，3.5寸版，这货是真TM的大，而且特别重，很佩服那些运维，不知道这么重的机子是怎么上架的。  
当时这个东西快递过来的时候，我抱着它走了一公里多到宿舍，回来之后手一直在发抖，第二天手还在抖。

既然这么重，用料肯定很足，来拆开看看

![内部](https://s1.ax1x.com/2018/09/22/iuKxdH.jpg)

这东西拆是很好拆，也不需要螺丝。我跟店家说要一个850W的电源，结果还是给我发过来一个570W的电源。电源倒是无所谓，懒得跟他计较了，先看看重点吧。

CPU是双路E5645，2011年的一款老U，TDP 80W，两颗U就是160W，再加上显卡75W，想把这些东西压住，散热压力也是好大的。所以我打算给CPU上水冷，至于显卡，750ti就没必要了，机箱内风墙顶着，够了。

## 水冷

某宝买的300块钱的水冷套件

![水冷套件](https://s1.ax1x.com/2018/09/22/iuMFQf.jpg)

还有彩灯，但是不是RGB

![彩灯](https://s1.ax1x.com/2018/09/22/iuMky8.jpg)

店家发给我的螺丝是M3螺丝和M4螺丝，但是扣具用的是M3.5的螺丝，店家那里也没有M3.5的，跑遍了五金店也没找到合适的，只能再从某宝买一套了。  
这套螺丝等了一个星期终于等到了，废话不多说，直接干。

![水冷扣具](https://s1.ax1x.com/2018/09/22/iuMCWt.jpg)

扣具什么的安装都很简单，先把水冷头连接好，然后对准了把双面胶粘上，然后再拧螺丝就可以了，如果有一套螺母固定螺丝就更好了，可惜我没有M3.5的螺母。

由于后来买的PWM调速器还在路上，所以暂时就先拿个变压电源充当调速器吧，如果水冷效果好的话，后面再买个高级点的调速器，直接用CPU温度控制。

{% mmedia "dplayer" "url:https://dl.sm9.top/Video/2018/%E5%86%B7%E6%8E%92%E8%B0%83%E9%80%9F.mp4" "id:F933308B39E58A8B894625BA29D6E962" %} 

这煤气灶火有点旺，压不住了。

机箱里面是没地方放了，只能放外面了。

![外置冷排](https://s1.ax1x.com/2018/09/22/iuM9JI.jpg)

![通电](https://s1.ax1x.com/2018/09/22/iuMiSP.jpg)

## 效果

R710本身噪音就不大，即使不装水冷，在CPU压力不大的情况下也不会特别响。搞完水冷之后，我准备再搞个静音箱把机子放起来，这样应该就几乎听不到一点声音了。

系统我装的是Win10 LTSB版，不会蛋疼的去搞什么服务器版本的，那鬼东西正常能用才怪呢，麻烦的要死。

水冷的效果嘛，暂时没搞过大的压力测试，只能简单的放张图看看了。

![10min高压](https://s1.ax1x.com/2018/09/23/iuMllV.png)

至于这台机器的使用感受，渲染视频是真的快，X264比我那个老掉牙的笔记本不知道快了多少倍。游戏几乎不怎么玩，只是偶尔玩玩LOL，反正LOL高品质是毫无压力，这台机子搞来不是打游戏的（其实打游戏的话，双路CPU毫无优势）。机子主要是用来渲染视频的，偶尔在上面跑跑程序什么的，4000能搞到这么一个东西，还是很不错的。