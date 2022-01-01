##前言
默认的example里面的peerconnection client是摄像头视频，因为我家里台式机没摄像头，也只有一台电脑，所以决定先在windows上实现桌面共享，最终效果如下：
<img width="1920" alt="d24d367ce87c4893c77088472341c25" src="https://user-images.githubusercontent.com/21189549/147846983-80bec70c-ce6e-4263-aff5-9e372d5a78db.png">
###步骤
实现比较简单，我们只需要在peerconnection client工程的基础上实现桌面抓屏的source部分即可，webrtc本身已经实现了各个平台的桌面和窗口抓屏在\src\modules\desktop_capture目录下，主文件是desktop_capturer.h，简单说一下window平台。
windows下的抓屏方式比较多，webrtc实现了**wgc**，**Magnifier**，**dxgi**，**gdi**四种抓屏方式，优先级依次排列，wgc和dxgi的抓屏效率比较高，但是wgc要求系统在1809版本以上。
需要注意的是webrtc实现了**FallbackDesktopCapturerWrapper**这样一个功能类，其包含主次两种抓屏方式，当前一种异常的时候会尝试使用后面的一种。

