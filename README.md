>近期在学习音视频，看了很多**雷神**([雷霄骅(leixiaohua1020)的专栏](https://blog.csdn.net/leixiaohua1020))
的文章，也做了相应的笔记，后面会陆陆续续发布。
虽然雷神不在了，但他留下了许多宝贵的财富，作为一名程序猿应向他学习,致敬！🙏🙏🙏

>欢迎加入技术交流群 
群号:  552340860

---------
### [原文链接](https://www.jianshu.com/p/b452d823b285)

# 一、视音频编解码技术零基础(理论总结)

### 1、视频播放器原理
&emsp;&emsp;视音频技术主要包含以下几点：封装技术，视频压缩编码技术以及音频压缩编码技术。如果考虑到网络传输的话，还包括流媒体协议技术。
&emsp;&emsp;视频播放器播放一个互联网上的视频文件，需要经过以下几个步骤：解协议，解封装，解码视音频，视音频同步。如果播放本地文件则不需要解协议，为以下几个步骤：解封装，解码视音频，视音频同步。他们的过程如图所示。

![视频播放流程图.jpeg](https://upload-images.jianshu.io/upload_images/1643859-037a1fd7fad12625.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

分析一下以上的每个流程:
##### 解协议
将`流媒体协议`的数据，解析为标准的相应的`封装格式数据`。视音频在网络上传播的时候，常常采用各种流媒体协议，例如HTTP，RTMP，或是MMS等等。这些协议在传输视音频数据的同时，也会传输一些`信令数据`。这些`信令数据`包括对播放的控制（播放，暂停，停止），或者对网络状态的描述等。解协议的过程中会去除掉信令数据而只保留视音频数据。例如，采用RTMP协议传输的数据，经过解协议操作后，输出FLV格式的数据。

##### 解封装
将输入的`封装格式的数据`，分离成为`音频流压缩编码数据`和`视频流压缩编码数据`。封装格式种类很多，例如`MP4，MKV，RMVB，TS，FLV，AVI`等等，它的作用就是将已经压缩编码的视频数据和音频数据按照一定的格式放到一起。例如，FLV格式的数据，经过解封装操作后，输出H.264编码的视频码流和AAC编码的音频码流。

##### 解码
将视频/音频压缩编码数据，解码成为非压缩的视频/音频原始数据。音频的压缩编码标准包含`AAC，MP3，AC-3`等等，视频的压缩编码标准则包含`H.264，MPEG2，VC-1`等等。解码是整个系统中最重要也是最复杂的一个环节。通过解码，压缩编码的视频数据输出成为非压缩的颜色数据，例如YUV420P，RGB等等；压缩编码的音频数据输出成为非压缩的音频抽样数据，例如PCM数据(`脉冲编码调制(Pulse Code Modulation,PCM)`)。

##### 视音频同步
根据`解封装模块`处理过程中获取到的参数信息，`同步`解码出来的视频和音频数据，并将视频音频数据送至系统的显卡和声卡播放出来。

#### 2、 流媒体协议
流媒体协议是服务器与客户端之间通信遵循的规定。当前网络上主要的流媒体协议如图表所示。

![流媒体协议.png](https://upload-images.jianshu.io/upload_images/1643859-13108fb0c4a75656.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* `RTSP+RTP`经常用于`IPTV`领域。因为其采用`UDP`传输视音频，支持组播，效率较高。但其缺点是网络不好的情况下可能会丢包，影响视频观看质量。因而围绕`IPTV`的视频质量的研究还是挺多的。
* `RTSP+RTP`系统中衡量服务质量可参考：[网络视频传输的服务质量（QoS）](http://blog.csdn.net/leixiaohua1020/article/details/11883393)

* 因为互联网网络环境的不稳定性，`RTSP+RTP`较少用于互联网视音频传输。互联网视频服务通常采用`TCP`作为其流媒体的传输层协议，因而像`RTMP，MMS，HTTP`这类的协议广泛用于互联网视音频服务之中。这类协议不会发生丢包，因而保证了视频的质量，但是传输的效率会相对低一些。

* `RTSP`规范可参考：[RTSP协议学习笔记](http://blog.csdn.net/leixiaohua1020/article/details/11955341)

* RTMFP是一种比较新的流媒体协议，特点是支持P2P。
比如[RTMP规范简单分析](http://blog.csdn.net/leixiaohua1020/article/details/11694129)，或者[RTMP流媒体播放过程](http://blog.csdn.net/leixiaohua1020/article/details/11704355)
* 相关工具的源代码分析：[RTMPdump源代码分析 1： main()函数[系列文章]](http://blog.csdn.net/leixiaohua1020/article/details/12952977)
* RTMP协议学习：[RTMP流媒体技术零基础学习方法](http://blog.csdn.net/leixiaohua1020/article/details/15814587)

### 3、封装格式
封装格式的主要作用是把视频码流和音频码流按照一定的格式存储在一个文件中。现如今流行的封装格式如下图表所示：
![封装格式.png](https://upload-images.jianshu.io/upload_images/1643859-7626f9a27a7b9f79.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
由表可见，除了`AVI`之外，其他封装格式都支持流媒体，即可以“`边下边播`”。有些格式更“万能”一些，支持的视音频编码标准多一些，比如`MKV`。而有些格式则支持的相对比较少，比如说`RMVB`。

### 4、视频编码

视频编码的主要作用是将视频像素数据（RGB，YUV等）压缩成为视频码流，从而降低视频的数据量。如果视频不经过压缩编码的话，体积通常是非常大的，一部电影可能就要上百G的空间。视频编码是视音频技术中最重要的技术之一。视频码流的数据量占了视音频总数据量的绝大部分。高效率的视频编码在同等的码率下，可以获得更高的视频质量。
视频编码的简单原理可以参考：[视频压缩编码和音频压缩编码的基本原理](http://blog.csdn.net/leixiaohua1020/article/details/28114081)
![视频编码.png](https://upload-images.jianshu.io/upload_images/1643859-af22ca9b6e2aea84.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
由表可见，有两种视频编码方案是最新推出的：VP9和HEVC。目前这两种方案都处于研发阶段，还没有到达实用的程度。当前使用最多的视频编码方案就是`H.264`。

#### 4.1 主流编码标准

`H.264`仅仅是一个编码标准，而不是一个具体的编码器，H.264只是给编码器的实现提供参照用的。

基于H.264标准的编码器还是很多,可参考：[MSU出品的 H.264编码器比较（2011.5）](http://blog.csdn.net/leixiaohua1020/article/details/12373947)

在学习视频编码的时候，可能会用到各种编码器（实际上就是一个exe文件），他们常用的编码命令可以参考：[各种视频编码器的命令行格式](http://blog.csdn.net/leixiaohua1020/article/details/11705495)

学习`H.264`最标准的源代码，就是其官方标准JM了。但是要注意，JM速度非常的慢，是无法用于实际的：[H.264参考软件JM12.2RC代码详细流程](http://blog.csdn.net/leixiaohua1020/article/details/11980219)

实际中使用最多的就是x264了，性能强悍（超过了很多商业编码器），而且开源。。编码时候可参考：[x264编码指南——码率控制](http://blog.csdn.net/leixiaohua1020/article/details/12720135)。编码后统计值的含义：[X264输出的统计值的含义（X264 Stats Output）](http://blog.csdn.net/leixiaohua1020/article/details/11884559)

Google推出的VP8属于和H.264同一时代的标准。总体而言，VP8比H.264要稍微差一点。有一篇写的很好的VP8的介绍文章：[深入了解 VP8](http://blog.csdn.net/leixiaohua1020/article/details/12760173)。除了在技术领域，VP8和H.264在专利等方面也是打的不可开交，可参考文章：[WebM(VP8) vs H.264](http://blog.csdn.net/leixiaohua1020/article/details/12720237)

此外，我国还推出了自己的国产标准AVS，性能也不错，但目前比H.264还是要稍微逊色一点。不过感觉我国在视频编解码领域还算比较先进的，可参考：[视频编码国家标准AVS与H.264的比较（节选）](http://blog.csdn.net/leixiaohua1020/article/details/12851745)

#### 4.2 下一代编码标准

下一代的编解码标准就要数HEVC和VP9了。VP9是Google继VP8之后推出的新一代标准。VP9和HEVC相比，要稍微逊色一些。它们的对比可参考：（1）[HEVC与VP9编码效率对比](http://blog.csdn.net/leixiaohua1020/article/details/11713041) （2）[HEVC，VP9，x264性能对比](http://blog.csdn.net/leixiaohua1020/article/details/19014955)

HEVC在未来拥有很多大的优势，可参考：[HEVC将会取代H.264的原因](http://blog.csdn.net/leixiaohua1020/article/details/11844949)

学习HEVC最标准的源代码，就是其官方标准HM了。其速度比H.264的官方标准代码又慢了一大截，使用可参考：[HEVC学习—— HM的使用](http://blog.csdn.net/leixiaohua1020/article/details/12759297)

未来实际使用的HEVC开源编码器很有可能是x265，目前该项目还处于发展阶段，可参考：[x265(HEVC编码器，基于x264)](http://blog.csdn.net/leixiaohua1020/article/details/13991351)[介绍](http://blog.csdn.net/leixiaohua1020/article/details/13991351)。x265的使用可以参考：[HEVC（H.265）标准的编码器（x265，DivX265）试用](http://blog.csdn.net/leixiaohua1020/article/details/18861635)

主流以及下一代编码标准之间的比较可以参考文章：[视频编码方案之间的比较（HEVC，H.264，MPEG2等）](http://blog.csdn.net/leixiaohua1020/article/details/12237177)

此外，在码率一定的情况下，几种编码标准的比较可参考：[限制码率的视频编码标准比较（包括MPEG-2，H.263， MPEG-4，以及 H.264）](http://blog.csdn.net/leixiaohua1020/article/details/12851975)

结果大致是这样的：
`HEVC > VP9 > H.264> VP8 > MPEG4 > H.263 > MPEG2`。

### 5、音频编码
`音频编码`的主要作用是将音频采样数据（PCM等）压缩成为音频码流，从而降低音频的数据量。音频编码也是互联网视音频技术中一个重要的技术。但是一般情况下音频的数据量要远小于视频的数据量，因而即使使用稍微落后的音频编码标准，而导致音频数据量有所增加，也不会对视音频的总数据量产生太大的影响。高效率的音频编码在同等的码率下，可以获得更高的音质。
音频编码的简单原理可以参考：[视频压缩编码和音频压缩编码的基本原理](http://blog.csdn.net/leixiaohua1020/article/details/28114081)

![音频编码.png](https://upload-images.jianshu.io/upload_images/1643859-d6ea862b281899f9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
由表可见，近年来并未推出全新的音频编码方案，可见音频编码技术已经基本可以满足人们的需要。音频编码技术近期绝大部分的改动都是在MP3的继任者——AAC的基础上完成的。

这些编码标准之间的比较可以参考文章：[音频编码方案之间音质比较（AAC，MP3，WMA等）](http://blog.csdn.net/leixiaohua1020/article/details/11730661)

结果大致是这样的：
`AAC+ > MP3PRO > AAC> RealAudio > WMA > MP3`

AAC格式的介绍：[AAC格式简介](http://blog.csdn.net/leixiaohua1020/article/details/11822537)
AAC几种不同版本之间的对比：[AAC规格（LC，HE，HEv2）及性能对比](http://blog.csdn.net/leixiaohua1020/article/details/11971419)
AAC专利方面的介绍：[AAC专利介绍](http://blog.csdn.net/leixiaohua1020/article/details/11854587)
此外杜比数字的编码标准也比较流行，但是貌似比最新的AAC稍为逊色：[AC-3技术综述](http://blog.csdn.net/leixiaohua1020/article/details/11822737)

### 6、现有网络视音频平台对比
现有的网络视音频服务主要包括两种方式：点播和直播。点播意即根据用户的需要播放相应的视频节目，这是互联网视音频服务最主要的方式。绝大部分视频网站都提供了点播服务。直播意即互联网视音频平台直接将视频内容实时发送给用户，目前还处于发展阶段。直播在网络电视台，社交视频网站较为常见。
![直播平台参数对比.png](https://upload-images.jianshu.io/upload_images/1643859-e1afd5f28283f3ca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看出，直播服务普遍采用了`RTMP`作为`流媒体协议`，`FLV`作为`封装格式`，`H.264`作为视频编码格式，`AAC`作为`音频编码格式`。采用`RTMP`作为`直播协议`的好处在于其被Flash播放器支持。而Flash播放器如今已经安装在全球99%的电脑上，并且与浏览器结合的很好。因此这种流媒体直播平台可以实现“无插件直播”，极大的简化了客户端的操作。封装格式，视频编码，音频编码方面，无一例外的使用了`FLV + H.264 + AAC`的组合。`FLV`是`RTMP`使用的封装格式，`H.264`是当今实际应用中编码效率最高的视频编码标准，`AAC`则是当今实际应用中编码效率最高的音频编码标准。视频播放器方面，都使用了Flash播放器。
![点播平台参数对比.png](https://upload-images.jianshu.io/upload_images/1643859-e402766cc132a41c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看出，点播服务普遍采用了HTTP作为流媒体协议，H.264作为视频编码格式，AAC作为音频编码格式。采用HTTP作为点播协议有以下两点优势：一方面，HTTP是基于TCP协议的应用层协议，媒体传输过程中不会出现丢包等现象，从而保证了视频的质量；另一方面，HTTP被绝大部分的Web服务器支持，因而流媒体服务机构不必投资购买额外的流媒体服务器，从而节约了开支。点播服务采用的封装格式有多种：MP4，FLV，F4V等，它们之间的区别不是很大。视频编码标准和音频编码标准是H.264和AAC。这两种标准分别是当今实际应用中编码效率最高的视频标准和音频标准。视频播放器方面，无一例外的都使用了Flash播放器。
