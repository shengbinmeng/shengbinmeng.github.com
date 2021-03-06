---
layout: post
title: adb server is out of date的原因及避免之道
date: 2013-10-18 20:00:0
category: 技术
tags: [Android]
---

执行Android开发包中的adb命令时出现了如下错误：

    C:\Users\Shengbin>adb devices
    adb server is out of date.  killing...
    ADB server didn't ACK
    * failed to start daemon *

<!--more-->
[这篇博客](http://blog.csdn.net/id19870510/article/details/8489486)中介绍了原因和解决方法。简言之，导致这个错误是因为adb daemon没有启动；之所以无法启动是因为别的线程占据了adb server需要用的端口。
到底是哪个程序会跟adb抢端口呢？

事实让人大跌眼镜！竟然是各种Android手机助手之类的东西。我遇到的是QQ里”安卓设备连接提醒“功能所启动的tadb.exe。看别人的案例有的是百度手机助手所启动的bdadb.exe，有的是HTC同步软件所启动的adb.exe，
还有金山、豌豆荚等各种杂七杂八的软件所启动的adb进程。它们用的都是Google官方开发包中的adb程序，只是简单改了个名而已，因此占用了同样的端口。找到这些进程然后关闭即可解决问题。

但如何避免类似的事情发生呢？最简单有效的方法就是：不安装任何助手之类的东西。不管是iOS还是Android系统，都有官方的使用和连接电脑方法，完全不需要这助手那助手的。
尤其是作为开发人员，你应该了解你所接触的系统并能够满足各种使用需要；助手之类的东西除了引入类似上面那样的诡异问题之外没有任何价值。

这涉及一个如何对待第三方工具的问题。我的看法是，一个成熟的系统一般都会提供有原生的解决方案，我们应该优先使用。仅对于那些系统没有满足的需求，才向别的工具寻求帮助。保持系统的简洁是一件非常有利的事情。
不要让很多你不了解的软件占据你的系统。

再扯的远一点，你的大脑也是一样。别让你的大脑成为别人思想的跑马场，就如同别让你的系统成为第三方工具肆虐的地方。
