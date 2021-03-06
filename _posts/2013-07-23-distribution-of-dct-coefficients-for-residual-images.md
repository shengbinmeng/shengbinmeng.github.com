---
layout: post
title: 残差图像DCT变换后系数的分布
date: 2013-07-23 12:00:01
category: 学术
tags: [信源编码, GGD, 建模]
---

在图像或视频编码中，要对残差进行离散余弦变换（DCT）变换，然后对变换系数进行压缩。残差是原本取值为0~255的图像像素值经过预测（预测可以简单地认为是一帧图像减去另外一帧类似图像）之后的差值。
由于是差值，所以残差可正可负。残差也构成一幅“图像”。下面的讨论中，所说的图像都是指残差图像。

<!--more-->

对于一个8x8的残差图像块，其中每个像素值可以认为是独立同分布的随机变量。由于是差值，我们认为该随机变量的均值为0，但它的概率分布具体怎样是不确定的。而且，各像素之间是否真的独立也值得怀疑。

对残差图像块进行DCT变换，从形式上看是对每个像素值进行加权求和。根据中心极限定理，多个独立同分布的随机变量（无论其分布是什么）之和接近于高斯分布。因此，认为图像块进行DCT变换后的每个系数近似符合高斯分布G。
中心极限定理要求随机变量是独立的，因此各个像素之间可能存在的相关性会影响上述近似的准确度。我们后面再分析这点。

由于变换前图像块的均值为0，所以变换后系数均值也为0，即G是零均值高斯分布。G方差则正比于变换前图像块中像素值的方差。
块中像素值的方差也是一个随机变量。通过统计多个块的像素方差，画出其直方图，我们发现像素方差可以被拟合为指数分布或一半的高斯分布。
若认为方差符合指数分布，则代入G中可以得出G变为了拉普拉斯分布；若认为方差符合半高斯分布，则代入G中可以得出G也近似为了拉普拉斯分布。因此，很多文献将变换系数建模为拉普拉斯分布。

但上述结论毕竟是近似得到的。拉普拉斯模型固然简单，但对于描述实际系数还是会有误差。更好的方法是采用广义高斯分布（GGD）作为模型。广义高斯分布中有两个参数alpha和beta，取不同的参数值可以简化为不同的概率分布。
拉普拉斯分布就是其中一个特例。GGD可以更好地对系数建模，但分析和使用它也变得复杂。

前面提到，将变换系数近似为高斯分布的准确度会受到像素间相关性的影响。事实上，即使是用广义高斯分布建模，也需要考虑像素间相关性对模型的影响。
根据DCT变换的性质，我们可以知道变换后系数的分布的方差正比于像素块的方差。但对于不同位置的系数，其分布的方差却并不一样，也就是这个正比的比例不一样。这正是由像素间相关性引起的。
假如所有像素都是不相关的，那么变换后不同位置的系数应该类似。因为不同位置的系数实际上代表了不同频率的能量，完全无关的像素变换后能量应该均匀分布在各个频带。
而实际上，往往低频系数的分布方差会较大，高频的较小。这体现在概率密度函数曲线上，就是低频系数对应的曲线宽度较大。

知道了变换系数分布的一些规律，我们就可以对这些系数进行更准确地建模，进而有针对性地设计更好的压缩算法。

本文的内容基于以下论文：  
[A Mathematical Analysis of the DCT Coefficient Distributions for Images](http://www.eee.hku.hk/optima/pub/journal/0010_ITIP.pdf)。
