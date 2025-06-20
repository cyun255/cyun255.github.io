---
title: 使用树莓派做旁路由
date: 2022-08-04
comments: false
categories: 玩转树莓派
tags:
	- 树莓派
	- OpenWrt
	- NAS
description: |
---

## 写在前面

最近暑假在家时间比较多，收拾东西的时候看到了几年前买的一个树莓派3B+。这玩意儿已经不知道被我仍在那吃灰多久了，就想拿出来玩一玩。

家里的路由器功能实在有限，就决定用树莓派做一个软路由，代替路由器做一些路由器处理不了的工作。

## 准备材料

- 树莓派
- 网线
- PC
- TF卡
- 读卡器

## 软路由

![家庭网络拓扑图](https://images.rescld.cn/202208042121068.png)

这是一个常见的家庭网络部署方式，光猫连接路由器WAN口，主要实现自动拨号上网等服务。

通过这种硬路由的方式可以快速的搭建起一个家庭局域网，但是路由器本身功能有限，不能够做一些DIY设置。这时候就可以选择搭建一个软路由，通过软件的方式帮助路由器做一些更加个性化的操作。

软路由的工作方式有两种
1. 将软路由作为主路由使用，直接接手路由器的全部工作，LAN口连接路由器做AP。
2. 将软路由作为旁路由使用，这种做法不影响原先的网络布局。软路由只是作为主路由的拓展提供一些额外的服务，走旁路由的网络设备需要手动设置网关地址（如下图所示）。

![旁路由拓扑图](https://images.rescld.cn/202208042122357.png)

个人认为家庭网络更适合做旁路由，因为有些操作也就自己用用，其他人用不到。其他人直接用主路由能正常联网就行了，有特殊需求的设备单独走旁路由。

## 开始制作

我直接使用了Github上开源的OpenWrt-Rpi项目，根据自己树莓派的型号下载对应的固件

- [Github](https://github.com/SuLingGG/OpenWrt-Rpi)
- [下载固件](https://doc.openwrt.cc/2-OpenWrt-Rpi/1-Download/)

![下面固件](https://images.rescld.cn/202208042122358.png)

下载好固件之后，将TF卡通过读卡器插入电脑，使用balenaEtcher把固件写入TF卡内。

balenaEtcher需要以管理员权限启动，否则可能会写入失败！！

![balenaEtcher](https://images.rescld.cn/202208042122359.png)

## 初始配置

固件写入成功之后，将TF卡插入树莓派中。拿一根网线连接树莓派与电脑，关闭电脑的WiFi，打开以太网设置，会看到连接的树莓派网络无法识别。

树莓派默认的IP地址是 `192.168.1.1` ，我们手动设置一下电脑静态IP。

![PC端静态IP设置](https://images.rescld.cn/202208042122360.png)

设置好静态IP后电脑就能够跟树莓派正常通信了，接着我们在浏览器中使用 `192.168.1.1` 进入 OpenWrt 的配置页面。

默认用户名 root，默认密码 password

![OpenWrt配置页面](https://images.rescld.cn/202208042122361.png)

## 配置接口

在网页中选择 `网络` -> `接口` ，会看到两个接口，一个LAN，一个VPN0。

我们修改一下LAN口配置，使用静态IP，网关跟DNS服务器都写成主路由的IP地址，然后保存&应用。

这时候把网线从电脑上拔下来插到主路由上，同时把电脑以太网IP地址改回自动获取，旁路由就搭建完成了！

![配置接口](https://images.rescld.cn/202208042122362.png)

## 网络存储

我们把一个存储硬盘插入树莓派中，打开网页中的 `系统` ->  `磁盘管理` 能看到系统会自动把硬盘挂载到 `/mnt` 文件夹下

![挂载硬盘](https://images.rescld.cn/202208042122364.png)

点击 `网络存储` -> `网络共享` 添加一个共享目录，通常文件权限644，文件夹权限755。为了文件安全，我这里不允许匿名用户操作。

手动编辑模板，在 `invalid users = root` 前面加一个 `#` ，或者删除这一行，允许使用root账号登入。

![网络共享](https://images.rescld.cn/202208042122365.png)

还有最后一步，设置一下samba共享时使用的root密码。

使用ssh连接树莓派，通过以下命令设置密码。

```sh
smbpasswd -a root 
```

到此为止，这个树莓派也能够当作一个NAS在局域网内共享硬盘。

我用家里的一个小米监控器试了一下直接全局扫描，打开 `设置` -> `存储设置` -> `NAS网络存储` ，也可以直接扫描到OpenWrt！

![NAS网络存储](https://images.rescld.cn/202208042122366.png)

## 科学上网

这个具体要怎么做就不展开说明了，就解决一个小问题。

当我们打开代理后，国外的网站基本上都能够秒开，但是国内的网站速度就特别慢，一个页面可能要好几分钟才出现……

打开OpenWrt后台 `网络` -> `防火墙` -> `自定义规则` ，在下面加入一行 `iptables` 命令，然后重启防火墙即可。

```sh
iptables -t nat -I POSTROUTING -j MASQUERADE
```
