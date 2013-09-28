---
layout: post
title: 考虑了运动校正的量化失真模型
date: 2013-09-25 17:00:0
category: 学术
tags: [Source Coding, 建模]
---
{% include JB/setup %}

<script type="text/javascript"
    src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>

对于视频编码，失真量化模型指得是失真与量化步长之间的关系。实际要解决的问题是如何准确估计在特定量化参数下的失真。
如果用分析型的方法，就是在给定的信源分布和量化方法下推导失真关于量化参数的表达式。如果用经验型的方法，则需要收集大量实际失真与对应量化参数的数据，然后拟合出一个函数模型。

<!--more-->
论文[《A Novel Analytic Quantization-Distortion Model for Hybrid Video Coding》](http://ieeexplore.ieee.org/xpls/abs_all.jsp?arnumber=4801619&tag=1)采用分析型的方法，提出了一个量化失真模型。其创新性在于考虑了运动校正对失真的影响，大致思路如下。

一个块的预测残差是该块的原始值减去上一帧的参考块的重建值。由于变换是线性的，因此残差变换后的系数也是同样的关系：

\\\[ R_k(q) = B_k^{orig} - B_{k-1}^{pred}(q). \\\]

该残差可以分解为两部分:

\\\[ R_k(q) = ( B_k^{orig} - B_{k-1}^{orig}(q) ) + ( B_{k-1}^{orig}(q) - B_{k-1}^{pred}(q) ) = I_k(q) + E_{k-1}(q), \\\]

其中\\( B_{k-1}^{orig}(q) \\)为参考块的原始值，它之所以与量化步长q有关，是因为参考块的选择（也就是运动矢量）取决于量化参数。

这两部分中，\\( I_k(q) \\)称为两帧间的变化， \\( E_{k-1}(q) \\)为上一帧的重建误差。假设它们都是零均值的且互相独立，那么由上式可以得出对应方差之间的关系：

\\\[ \sigma_\{\{R_k}}^2(q) = \sigma_\{\{I_k}}^2(q) + \sigma_\{\{E_{k-1}}}^2(q). \\\]

\\( I_k(q) \\)主要取决于运动矢量的准确性，而运动矢量的准确度受量化步长q的影响。q越大， 运动矢量准确度越低，则\\( I_k(q) \\)的幅值和方差就越大。文中用一个线性关系来描述之，于是有：

\\\[ \sigma_\{\{I_k}}^2(q) = (1+\gamma(q-1))\sigma_\{\{I_k}}^2(1), \\\]

其中\\( \\gamma \\)为参数，可通过实际数据训练得到。

\\( E_{k-1}(q) \\)主要是由对残差进行量化导致的，因此, \\( \sigma_\{\{E_{k-1}}}^2(q) \\)就是不考虑运动估计时的量化失真，可根据残差的分布以及所用的量化模式推导出来。

由于考虑了运动估计，也就是引入了\\( I_k(q) \\)，上述失真模型比单独考虑残差量化的失真更准确。实验结果也说明了这一点。

注：本篇博客使用了[MathJax](http://www.mathjax.org)，数学公式的渲染可能需要一定时间。如果公式显示不正常，可尝试刷新页面。