---
title: 一次音频编码的优化尝试
date: 2017-03-20 12:00:00
category: 技术
tags: [音频, 多线程]
---

最近在优化一个多媒体处理过程时，发现音频编码模块耗时很多，就试图优化之。

<!--more-->

音频与视频一样，也有帧的概念。经分析发现，当前实现中一个音频帧包含1024个采样点（sample）。
每个采样点是两通道（channel），每通道是16位深（2字节），因此每个采样点是4个字节，每帧音频就是4096个字节。
采样率是44100Hz，则一个音频帧的持续时间是1024/44100=0.023s，23ms。
视频帧率是30FPS，则一个视频帧的持续时间为1/30=0.033s，33ms。
这样的话，同样时长内音频帧的数目大约是视频帧的1.4倍。

对于10s时长、300帧视频的情况，音频就有420帧。与视频一样，音频编码也是每次送给编码器一帧数据，所以按当前实现就要调用420次音频编码。
每次音频编码耗时10ms，音频编码花费的总时间为4.2s。我们希望减少这个时间。

首先想到的途径是减少音频编码的调用次数。既然每次编码的单位是一帧，如果能使一帧包含更多的音频数据，就能减少帧数和调用次数。
例如，如果使一个音频帧包含2048个采样点，就能使调用次数减半。我尝试实现了这个方法，调用次数确实减半了，但结果音频总是不正确。

寻找原因发现：AAC音频编码标准的低复杂度档次（AAC-LC）规定编码时每一帧只能包含1024个采样点。我们填入2048个采样点的话，后面的一半数据被编码器无声无息地忽略了。
如果换用AAC的高压缩率档次（AAC-HE），则一帧包含的采样点数目可以增大为2048（这也是标准的规定），且得到正确的结果。
但可惜的是，这样改后虽然调用次数减半了，单次调用的耗时却加倍了（而且AAC-HE比AAC-LC复杂度高，单次调用的时间甚至比加倍还要长点儿）。
于是可以得出结论，音频编码是一个相对线性的处理过程，无法通过减少调用次数来减少耗时。

进一步寻找其他加速方案时发现，我们目前用的AAC编码器在编码时只有单线程，CPU只有单核在工作，利用不充分。
因此，可以减少整体耗时的一个方向就是另开一个线程用于音频编码，与其他处理模块并行起来，以求达到最大的CPU利用率。
根据目前了解的情况，在音频编码器内部实现多线程并行估计是没戏。
 
另外，当前实现中音频编码的码率是128kbps。测试发现，将码率降为64k时耗时变化不大，但从64k降为32k就能节省一半时间（我对音频编码不专业，这现象其实让我很奇怪）。
如果允许牺牲音频质量，降码率也是可以加速的一个方向，不过就需要权衡了。

最后，实在不行还可以换个编码器嘛！