---
title: 又入手一台小JJ
date: 2019-04-07 16:30:09
tags: [建站笔记, 随便水水]

keywords: Sakura Frp, 守护进程, PM2, Supervisor
comments: true
---

上个月花了购置了一台小jj用作服务器，简单记录了一下装配过程，正好清明节假干完两天的活，休息一天，就写一点出来。

<!-- more -->

## 硬件配置

|   硬件    |  配置  |  价格   |
|:-------:|:-------:|:-------:|
| 机箱主板电源 | 惠普800G1小主机，Q87主板 | 315 |
| CPU | 魔改4870HQ | 598 |
| 内存 | 二手杂牌 DDR3L 1600 * 2（有一条是从以前笔记本上拆下来的） | 158 + 0 |
| 硬盘0 | 建兴 128G mSATA接口 | 146 |
| 硬盘1 | 西数500G（笔记本拆机） | 0 |
| 总计 |    | 1217 |

硬件上来说就是这样了，这肯定不是最实惠的方案，也不要跟我说XXX更省钱了，反正我没翻车，连续开机29天没重启，照样跑的稳稳的。
 
选择机箱的时候，考虑体积价格和拓展性，最终选择了惠普的800G1，真的很小的一个机箱
![整体](https://s2.ax1x.com/2019/04/07/AhyDqf.jpg)  

而CPU在魔改4带BGA封装U和E3上权衡了很久，因为我不确定这机器上能不能上E3，因为惠普那尿性的微码问题，包括我现在也没有解决，每次开机都要按F1才能进，虽然网上有人放出了修改过的微码，但是我的编程器坏了（估计是太久没用了，再加上两次搬家，旅途劳顿，挂掉了），懒得再去搞了，按F1就按F1吧。

![CPU](https://s2.ax1x.com/2019/04/07/AhyBsP.jpg)  

16G内存，目前来说稍微有点浪费，但是浪费就浪费吧，但是指不定以后会在上面跑什么呢。

## 系统
先装个Windows，测试一下整机数据，然后再去搞服务器环境

服务器系统选择没什么说的，自己一直在用Debian，自然就直接上Debian了，装完之后又上了宝塔邪教，这东西很方便，因为测试环境配置什么的都要经常改，所以很适合使用宝塔，别BB什么什么的，我点几下，一分钟就能解决的事，有必要花五分钟去写一个配置文件嘛。

## 网络

网络的话，我这里是电信百兆，上行带宽最快能到2M/S这个水平吧，其中比较难解决的是外网访问问题，虽然这有公网IP，但是没有80端口很难受啊，这时就想到了以前在V2见过的Sakura Frp，简单记录一下使用方法

首先是注册一个账号 [https://www.natfrp.org/](https://www.natfrp.org/) ；  
登录之后在`映射列表`里先创建一个SSH通道，可以参考这里[https://www.zerobbs.net/thread/50](https://www.zerobbs.net/thread/50)；  
然后按照 [https://www.zerobbs.net/thread/6](https://www.zerobbs.net/thread/6) 的方法下载客户端，解压完停下来就好，后面就不需要继续了；  
先测试一下连接，首先直接启动frp服务`./Sakura_frpc_linux_amd64` 然后会让你输入账号密码，具体参考官方论坛；  
上一步如果提示连接成功了，咱们再进行下一步测试，记住了自己选择的服务器和第二步选择的端口，然后连接一下SSH服务看一下 `ssh root@s21.natfrp.org -p xxxx` 其中 `s21.natfrp.org` 是你选择的服务器，`-p` 参数是你映射的端口，如果可以联通的话，就算是测试成功了，咱们就可以进行下一步了；  
![SSH连接成功](https://s2.ax1x.com/2019/04/07/AhUbPx.png)  
后面要解决的是守护进程的问题，我曾经用两种不同的守护工具实现过，都是对应不同的实际环境，这里简单的介绍一下怎么写配置文件。

首先是 `Supervisor`

怎么安装和使用，可以看完以前的文章[.net core 程序示例](/2018/run-dotnetcore.html)，这里简单说一下配置文件就可以了

```
[program:frp]
command=/www/frp/Sakura_frpc_linux_amd64 --su={用户名} --sp={密码} --sid={前面命令行启动时候的编号}
user=root
stopsignal=INT
autostart=true
autorestart=true
startsecs=3
stderr_logfile=/www/frp/frp.log
stdout_logfile=/www/frp/frp.out.log
```
里面的变量替换成你自己的（没用括号），路径也换成你自己的

另一种方法是用 `PM2`

首先宝塔面板自带了pm2管理器，可以在软件管理里直接安装，如果没用使用宝塔面板的话，可以选择先安装Node和npm，然后用npm安装pm2，具体怎么安装，可以百度

在frp解压的目录下新建一个文本文件
```
cd /xxxxxxxxxx
touch ./frp.json
```

然后修改配置文件 `vim ./frp.json`

```json
{
  "apps": [{
    "name": "frp",
    "script": "Sakura_frpc_linux_amd64",
    "cwd": "/www/frp/",
    "autorestart": true,
    "node_args": [],
    "args": ["--su=用户名", "--sp=密码", "--sid=8"],
    "env": {
    }
  }]
}
```

其中`script`是要执行的命令，`cwd`是路径，换成你自己的，后面的参数同样换成你自己的，写好之后

```
pm2 start frp.json
```

再确定一下有没有启动
```
pm2 list

pm2 show 0
```
![pm2状态](https://s2.ax1x.com/2019/04/07/AhDPv6.png)

再次用ssh连接测试，看看能不能在外网连上，如果没问题的话，守护进程的问题就算是解决了。

再添加几条隧道，比如http和https隧道，这里就不再啰嗦了，按照官方论坛上的教程来就可以了。

## 服务

目前这台小JJ主要用来测试，搭建了几个比较简单的服务，直播服务（主要在局域网内测试），二维码生成服务 [https://qr.sm9.top/api/qr?tt=参数](https://qr.sm9.top/api/qr?tt=参数)，函数图像生成服务（自己测试使用，还没写完），以及日常开发过程中的测试

## 总结

闲的蛋疼，又搞了台机器回来吃灰了。










