# Android 多媒体开发

> 原文：[`c.biancheng.net/android/60/`](http://c.biancheng.net/android/60/)

本章介绍了 Service 的相关知识，涉及 Service 的生命周期和使用方法。Service 是一种与 Activity 类似的程序，不同的是它没有用户界面，只能在后台运行。Service 适合执行一些较为耗时的工作。当 Service 被启动后，它会一直在后台运行，直到调用 Service 的 stopService() 方法才终止服务。例如，本章中实现的后台音频播放程序，当离开音频播放的主界面时，音乐还是会一直播放。

本章还简单介绍了 BroadcastReceiver 的使用方法。BroadcastReceiver 是 Android 系统中的广播接收者，通过 BroadcastReceiver 可以轻松实现对 Android 系统中特定事件的处理，例如当有来电、电池电量发生变化等事件发生时，可以使程序员通过自己开发的应用程序对事件进行处理。

此外，本章介绍了多媒体开发相关的知识，如视频播放、音频播放、视频和音频的录制、使用照相机拍照等，其中涉及 Android 系统的硬件编程。读者可以举一反三，对 Android 系统提供的硬件资源进行更多的开发。