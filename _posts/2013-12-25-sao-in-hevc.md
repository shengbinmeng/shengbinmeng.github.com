---
layout: post
title: HEVC里的SAO
date: 2013-12-25 13:00:00
category: 学术
tags: [HEVC]
---
{% include JB/setup %}

在最新的视频编码标准HEVC中，环内滤波除了去块滤波外，还增加了一个被称为Sample Adaptive Offset（SAO）的过程。

<!--more-->
##SAO简介

SAO主要思想是通过修正重建图像来减少其与原始图像的偏差。SAO以CU为单元进行处理。对于一个CU内的所有像素，根据一定的标准将它们分为几个组，每个组都算出一个修正值，用这个值来修正重建像素从而减少与原始图像的误差。
这里涉及两个问题，第一是如何分组，第二是如何确定修正值。第二个问题可用[RDO方法](http://en.wikipedia.org/wiki/Rate%E2%80%93distortion_optimization)解决。因为修正值需要写在码流中传给解码器，所以要考虑它们占的比特，
RDO正适合这种权衡比特数和失真的问题。对于第一个问题，在最终的标准中给出了两种分组方法：Edge方法和Band方法。

##按Edge方法分组

Edge方法是SAO中的两个分组方法之一。在这种方法中，根据一个像素与其相邻像素值的大小关系来分组。这种方法下按照选取相邻像素的方向又分为四个类别，如下图所示。

![](/images/2013-12-25-sao-edge-class.png)

对每一个CU，将通过RDO的方法从上面的四个类别中选一个，也就是说，确定选取相邻像素的方向。

接下来，按照每个像素（c）与其相邻像素（a、b）的值的大小关系来将像素分为5组。实现上，我们可以按`sign(c-a) + sign(c-b)`的值来分组，其中sig表示取符号函数，值域为{+2， +1，0，-1，-2}。

对于`sign(c-a) + sign(c-b)`值为0的像素组，不进行SAO。对于其他四个像素组，每一组确定一个偏差值，使其尽可能接近原始图像。这4个偏差值将被写入码流并传给解码器。

##按Band方法分组

Band方法只根据像素本身来分组。对于0~255的像素值，将其分为32个带，每个带覆盖8个值。这32个带就是32个组。但为了减少所需比特，只对其中4个组进行SAO（这样传给解码器的偏差值的个数就与Edge方法相同了）。
用RDO的方法从32个组中选取连续的4个，将第一个组的位置以及这四个组的偏差值写入码流并传给解码器。

##SAO语法设计

SAO是以CU为单位的。对每个CU来说，需要写入码流的有SAO分组类型、偏差值等一系列信息。为了尽可能节省比特，设计语法时考虑了SAO Merge的情况，也就是说，允许当前CU重用上方或左侧CU的SAO参数。

此外，为进一步减少头信息和复杂度，规定两个色度分量（Cb、Cr）的SAO参数一样，只需要传一次即可。

##SAO实现上的一些考虑

SAO操作虽然涉及所有像素，计算量大，但由于其运算都比较规整，可以采用SIMD指令来进行优化。例如，对于Edge模式中的分组，可以用一条指令同时处理多个像素与相邻像素做差并将差值符号相加的操作。
Band模式中，将像素按值划分为32个带可以通过移位来实现，而取每个分组的偏差值可以用NEON中的查表指令来实现。

##更多

更多关于HEVC中SAO的信息可以参见论文[《Sample Adaptive Offset in the HEVC Standard》](http://ieeexplore.ieee.org/xpl/abstractAuthors.jsp?arnumber=6324411)。这篇论文里也给出了更多关于SAO实现上的考虑并提供了有关SAO提升视频质量的实验结果。