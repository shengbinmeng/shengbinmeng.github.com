---
layout: post
title: 话说IIS
date: 2009-04-15 12:00:05
category: 朝花夕拾
tags: [梦之栖居, 2009年]
---

> 这篇文章属于[《梦之栖居》](/posts/where-the-dreams-reside/)系列，写于2009年。
	
<!--more-->

由于学习做网站，需要安装服务器程序。记得之前的PHP和Apache搭档的配置就花了我很多时间。这次要ASP.NET搭配IIS（Internet Information Service），仍然是比较麻烦。最后终于搞定了，这里总结经验。

IIS是微软Windows系统自带的，本来可以直接在“添加组件”中轻松地装上。但可能是因为我这盗版的系统的问题（？），在安装过程中总是提示找不到某.dll文件。这样的话，靠自己的机子估计肯定装不上了。于是到网上下载别人的IIS安装程序包，把需要的文件拷到适当的地方即可。利用下载的文件可以安装成功。另外发现，甚至有专门下载.dll文件的站点，网络真是……以后要好好利用。

安装好IIS之后，经尝试打不开asp文件，只能打开html的。上网一查，原来这是个普遍问题。最主要的问题在于“密码不同步”。于是按照别人介绍的各种方法开始折腾，又是cmd命令行又是管理工具的，搞了两个半天也没搞定，甚至中间还出现了连html文件都打不开的情况！无奈之下，只好用第三十六计：重装IIS。出乎意料的是，重装之后一切正常了。那些乱七八糟的错误就像没发生过一样。早知道……

之所以最后才选择重装是因为网上普遍反映重装也解决不了问题。我猜可能他们重装之前没有把前次遗留的痕迹清楚干净。比如要删除IIS创建的两个用户、甚至需要删除某些IIS写入的注册表项等。以后再解决软件问题，可以参考“清除——重装”的模式。但同时，这样似乎少了很多从错误中学习的机会。

最后想说一下，IIS确实很强大。不仅提供www站点的服务，还可用来建立ftp及邮件服务器。不得不承认，微软的东西确实不错。相比之前接触的Apache HTTP Server，不仅功能多感觉也更方便。但毕竟，后者是免费的，而IIS算是收费的吧（当然对像我这样的盗版使用者除外）。