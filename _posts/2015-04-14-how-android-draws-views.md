---
layout: post
title: Android如何绘制视图
date: 2015-04-14 12:00:00
category: 技术
tags: [Android, 编程]
---
{% include JB/setup %}

Android开发者网站上的一篇[文档](https://developer.android.com/guide/topics/ui/how-android-draws.html)介绍了Android系统是如何绘制视图的。

<!--more-->

绘制过程从视图树的根节点开始，自顶向下依次完成。在这个过程中，每个父节点（ViewGroup）负责要求它内部的所有子节点（View或子ViewGroup）进行绘制，也就是调用子节点的draw()方法。
每个节点都负责绘制自己，也就是实现自己的draw()方法。

为了实现draw()方法，也就是画自己，视图需要知道它在哪儿画、画多大区域。这通过一个从根节点开始的自顶向下的布局（layout）过程实现。
在这个过程中，每个父节点负责调用其所有子节点的layout(int left, int top, int right, int bottom)方法，通过这个方法的四个参数指定了子节点相对父节点的左、上、右、下的位置，
也就是告诉了子节点它在哪儿画，画多大区域。

为了实现上述布局过程，父节点需要与其所有子节点沟通，因为父节点并不知道子节点的内容，自己无法确定究竟怎样布局。
这个“沟通”过程称为测量（measure），其实质是父节点与每个子节点协商尺寸信息，从而让父节点能够统筹全局，正确或者最优的进行布局。
父子节点间互通尺寸信息有两个途径：

1. 每个视图通过布局参数（LayoutParams）属性来告诉它的父节点自己希望的尺寸和位置，例如`MATCH_PARENT`、`WRAP_CONTENT`等。

2. 父节点调用其子节点的measure(int widthMeasureSpec, int heightMeasureSpec)方法。这个方法的参数指定了对子节点尺寸的限制；而这个方法执行完时，
子节点必须设置好自己的measuredWidth和measuredHeight，供父节点布局时读取并参考。

可以看到，在布局时父节点会征询子节点的意见，但最终还是通过调用layout方法自主决定每个子节点的大小和位置。

总结一下，整个绘制过程是一个多次自顶向下遍历视图树的过程。第一次，进行测量（调用measure方法）；第二次，进行布局（调用layout方法）；第三次，进行实际绘制（调用draw方法）。
当然这些方法调用多少次，以及在视图树的哪部分调用是根据绘制需求灵活决定的。

测量、布局、绘制（相当于施工），小机器人有条不紊地建造出了用户看到的应用程序界面。