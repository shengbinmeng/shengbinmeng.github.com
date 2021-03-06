---
layout: post
title: 开发和分发iOS应用
date: 2013-09-09 20:00:0
category: 技术
tags: [Xcode, iOS]
---

本文介绍iOS应用开发和分发的方式和流程。前提是必须加入苹果公司的iOS开发者项目，以获得证书（Certificate）。

<!--more-->

### 证书
从苹果公司的iOS开发者项目获得的证书有两个，分别为开发（Development）证书和分发（Distribution）证书。它们的用途主要有：
1. 对程序签名（Sign the app）
2. 生成描述文件（Generate Provisioning Profile）

### 描述文件
描述文件（Provisioning Profile）有两类，一是开发类，一是分发类，分别由开发证书和分发证书生成。
生成描述文件时，除了证书，还需要指定应用程序的App ID和设备的Device UUID。
也就是说，描述文件中包含了对于哪些程序可以安装在哪些设备上的描述。

iOS应用程序在设备上安装时，需要设备上已经存在有效的描述文件（有效指的是该描述文件中含有当前安装的程序ID和当前设备ID），否则无法安装。

装有开发类描述文件的设备只能安装用开发证书签过名的程序。
装有分发类描述文件的设备只能安装用分发证书签过名的程序。

### 开发
iOS开发一般采用苹果公司提供的集成开发环境Xcode。
在程序生成（Build）的时候，Xcode需要对程序进行签名，否则将生成失败（只是为iOS设备生成的时候需要签名；如果为模拟器生成则不需要）。此时这个签名需要用开发证书来签。
生成之后的程序若要在真机上调试，就要求用来调试的机器里必须存在有效的开发类描述文件。
所以，一个新的设备连接到电脑后，需要先在Xcode里为其生成并安装开发类描述文件（在Organizer里点击“用于开发”），之后才能用来调试。

开发类的描述文件一般由Xcode自动管理即可，无需特别关注。

### 分发
分发iOS应用有两种方式，要么提交到苹果应用商店，要么自己发布安装包，分别称为App Store分发和Ad Hoc分发。
不管用哪种方式，都要先进行程序打包（Archive）。
在程序打包的时候，Xcode也要对程序进行签名。这时的签名就需要用分发证书来签。签完名并打好包的程序就可以进行分发了。

如果提交给App Store，在Xcode里可以非常简单的完成，这里不再详述。如果要自己发布安装包，则相对复杂一些，因为涉及到自行生成描述文件的问题。

前面提到，只有用户的设备上安装了有效的分发类描述文件，才能安装你的程序包。
为了得到有效的描述文件，需要知道用户设备的UUID。拿到设备ID后，可以到开发者网站去，
选择分发证书，选择你要发布的程序的App ID，选择你收集到的设备ID，用这些来生成一个有效的分发类描述文件。

生成之后，在Xcode里选择Ad Hoc分发，Xcode会让你提供描述文件，并把这个描述文件嵌入程序包，然后导出一个xxx.ipa文件。
将这个ipa文件发给用户，他们便可以通过iTunes来安装程序包。安装前，包里面的描述文件会被拷贝到用户的设备上，从而保证了程序正常安装和运行。
