---
title: VirtualBox网卡问题
date: 2016-10-16 12:00:00
category: 技术
tags: [VirtualBox]
---

VirtualBox虚拟机网卡可以设置为不同的模式，其中最常用的是NAT（网络地址转换）模式和Bridge（桥接）模式。

<!--more-->

我喜欢用桥接模式，因为它会给虚拟机分配一个与宿主机在同一网段内的IP地址。这样虚拟机可以不依赖宿主机的网络，相当于处在与宿主机同等的地位。
说白了，这样使得虚拟机更像一台真机器。

不过今天在一个Wi-Fi环境中使用虚拟机时，发现桥接模式无法上网，改成NAT模式后就可以。我想起自己之前大多是在连有线网的机器上用虚拟机，就推测是否桥接无线网卡有特殊的要求。
我的第一反应是需要在虚拟机的操作系统里安装一个无线网卡驱动。因为桥接可以认为是让虚拟机有了一个网卡硬件，相应地需要有驱动才行。但自己尝试安装了几个驱动也没用。之后经过各种搜索也没能解决这个问题。

我放弃了折腾，先用NAT模式工作了半天。后来回到家里又尝试了一下，竟然桥接成功正常上网了，没有任何问题！

这让我非常郁闷。仔细推断一下，其实我的虚拟机和宿主机设置都是没问题的，导致无线桥接不能正常上网的很可能是所处的网络环境（例如无线路由器的设置、网络政策等）。
联想到之前的[Mac OS X主机名称变化](/posts/mac-os-x-hostname-change)事件，我对外部环境因素的“警惕性”更加高了。
也就是说，要尽早考虑到外部环境的变化可能是导致出现问题的原因，这样或许能避免很多不必要的弯路。