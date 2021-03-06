---
title: 编码已经编码过一次的视频
date: 2017-05-12 12:00:00
category: 技术
tags: [x264, x265]
---

编码一个已经被编码过一次的视频，情况会怎样呢？我最近做了相关的一次验证，结果还挺有价值。

<!--more-->

事情是这样的，我们要对比评测x264与x265的编码性能。但这里的编码不是对原始视频进行压缩，而是对本来已压缩过的MP4文件进行转码。
在对比中发现，对于有些MP4视频文件，x265的RD性能比x264领先（当然耗时更多），这也是符合预期的。但对于另外一些视频文件，x265竟然比x264性能低不少（虽然耗时也多）。

对此，同事甲的解释是，x265现在还处于成长期，对于某些特定内容的视频编码优化没做好。他还指出x265编不好的这几个视频都是韩国人跳舞的MV场景，比较难编。
但我对此表示怀疑。毕竟x265是实现的新一代视频编码标准，而且也已经开发了这么久，不该比x264差才对。

我的解释是，这些视频可能对x264来说异常好编。为什么呢？我猜是因为这些视频本来就是由x264编出来的。被x264处理过的视频，再次由x264处理的话，就会容易很多。
而对x265来说却是第一次处理，不公平。但这个解释同事甲不认同，他觉得编码器性能与输入源被谁处理过关系不大。

在讨论的过程中，我先形成了这样一个理论：原始视频先被编码器A编码一次，重建结果再被编码器A编，那么第二次编码的RD性能会高于第一次。
对此同事甲辩驳说，如果这样的话，那编第三次、第四次呢，难道每一次都会比前一次RD性能高？我觉得可能真就是这样。

在上述这个理论的基础上，自然就能引出第二个理论：被编码器A编过的视频，再被编码器A编，就比用其他编码器编要占优势。我觉得这才是解释我们遇到的x265不如x264的原因。

当然，这些理论都是可以验证的。我们做了两组实验。

**实验一**

对比下面这三个命令打印出的RD值（码率和PSNR），看是否RD性能逐渐提高。我预言是。

	ffmpeg -video_size 464x828 -i origin.yuv -vcodec libx264 -psnr 1.h264
	ffmpeg -i 1.h264 -vcodec libx264 -psnr 2.h264
	ffmpeg -i 2.h264 -vcodec libx264 -psnr 3.h264
	
结果这三条命令打印出的RD值依次为：

	1425 kbps, 39.7 dB
	1261 kbps, 43.4 dB
	1146 kbps, 44.8 dB
	
可以明显看到码率逐步降低且PSNR逐步升高，也就是RD性能逐渐提高。理论一得到了证实。

**实验二**

对x265不如x264的那几个MV视频，先编出一份x265的版本，然后以此作为输入再次对比x264和x265的压缩效率。
虽然是同样的视频内容，我预言这次x265将明显优于x264。

结果与预言完全一致。

为了再次确认，我用`mediainfo`工具查看了下原来的对比中那些x265不如x264的视频，果然它们全是x264编出来的。
	
至此，事情完全搞清楚了。从中我们也得到以下启示：首先，完全科学地对比编码器性能，自然要用原始视频作为输入。
原始视频信号中高低频分量都在，经过压缩后就丢失了很多高频分量，已经不适合用于编码器的评测了。
其次，即使要针对转码过程（输入是压缩过的视频）进行评测，为公平起见，也不要选被评测对象处理过的视频。
就像这次，我们要对比x264和x265在转码中的性能，就该选取其他编码器处理过的视频（例如硬件编码出来的）作为转码输入。
