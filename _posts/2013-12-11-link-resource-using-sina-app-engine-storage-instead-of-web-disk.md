---
layout: post
title: 使用SAE存储服务替代网盘来链接资源
date: 2013-12-11 22:00:00
category: 技术
tags: [Web, SAE]
---

最近要在博客上放音乐，就去找了找在网络上免费存储数据（最典型的如图片和音乐）并使用其直接链接的方法。
看大家都是把数据存在网盘上，然后折腾各种获取网盘外链的方法和工具，并疲于应对网盘提供商的封禁措施。
最终并没有找到我可以接受的解决方案。

于是我另辟了一条蹊径：使用新浪云平台（Sina App Engine，简称SAE）提供的存储服务。

<!--more-->

[SAE](http://sae.sina.com.cn/)作为国内最早的云计算平台，其中的[存储（Storage）服务](http://sae.sina.com.cn/?m=devcenter&catId=204)
可为个人开发者免费提供高达100G的存储容量。去接触一下SAE，然后使用其中的Storage服务，你就会得到像下面这样的直接链接了。
完全不用再去折腾网盘的外链提取，而且有充足的存储空间可用。

<http://shengbin-static.stor.sinaapp.com/lian-lian-feng-chen.mp3>

嗯，对于一个追求URL可读性的人来说，上面的链接看上去就挺美。

云计算是个好技术，新浪SAE也是值得尊敬的平台，只用它来存储数据其实并非什么光彩的事情，所以我就不公布细节了。
另外，别的应用引擎应该会提供类似的服务，例如百度的BAE，也不妨一试（虽然我没用过）。