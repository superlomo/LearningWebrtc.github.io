## 前言

默认的example里面的peerconnection client是摄像头视频，因为我家里台式机没摄像头，也只有一台电脑，所以决定先在windows上实现桌面共享，最终效果如下：
<img width="1920" alt="d24d367ce87c4893c77088472341c25" src="https://user-images.githubusercontent.com/21189549/147846983-80bec70c-ce6e-4263-aff5-9e372d5a78db.png">

### 分析
实现比较简单，我们只需要在peerconnection client工程的基础上实现桌面抓屏的source部分即可，webrtc本身已经实现了各个平台的桌面和窗口抓屏在\src\modules\desktop_capture目录下，主文件是desktop_capturer.h，简单说一下window平台。
windows下的抓屏方式比较多，webrtc实现了**wgc**，**Magnifier**，**dxgi**，**gdi**四种抓屏方式，优先级依次排列，wgc和dxgi的抓屏效率比较高，但是wgc要求系统在1809版本以上。
需要注意的是webrtc实现了**FallbackDesktopCapturerWrapper**这样一个功能类，其包含主次两种抓屏方式，当前一种异常的时候会尝试使用后面的一种。

### 实现
source和sink的实现部分我们主要参考了以下博文[WebRTC Native开发实战之数据采集--屏幕](https://www.cnblogs.com/xl2432/p/13856533.html)，后面我们只需要实现一个track即可。
这里需要学习的是source，sink和track的概念。
具体diff文件见：

### 后记
总的来说比较简单，但是翻看webrtc的代码，发现抓屏部分的效率并不高
1. 原始的比如wgc抓屏是由windows事件驱动的，但是webrtc却为了封装，却将所有的抓屏方式封装为用户手动调用captureFrame函数，这样势必导致性能损失和可能的丢帧
2. wgchedxgi抓屏本来抓到的是d3d的texture，但是看起来webrtc也是为了封装，直接将其导入到内存内的yuv或者rgb进行传输，并且不时看到使用libyuv进行格式转换的代码，这样也会导致性能的大大降低
3. 就目前看到的，如果想实现高质量的抓屏编码，还是得自己实现，比如**nvcapture** + **nvenc** 或者**wgc** + **nvenc**

