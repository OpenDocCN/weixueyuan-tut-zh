# Android 四大核心组件：Activity+Service+BroadcastReceiver+ContentProvider

> 原文：[`c.biancheng.net/view/2918.html`](http://c.biancheng.net/view/2918.html)

Android 作为一个移动设备的开发平台，其软件层次结构包含操作系统 (OS)、中间件 (MiddleWare) 和应用程序 (Application)。

Android 的应用程序通常涉及用户界面和用户交互，这类程序是用户实实在在能感受到的。目前 Android 本身提供了桌面、联系人、电话和浏览器等众多的核心应用，同时还允许开发者使用应用程序框架层的 API 实现自己的程序

Android 系统没有使用常见的应用程序入口点的方法（例如 C 语言中的 main() 方法），应用程序是由组件组成的，组件可以调用相互独立的基本功能模块。根据完成的功能不同，Android 划分了 4 类核心组件，即 Activity、Service、BroadcastReceiver 和 ContentProvider，各组件之间的消息传递通过 Intent 完成。

## Activity

Activity 是 Android 应用程序核心组件中最基本的一种，是用户和应用程序交互的窗口。

在 Android 应用程序中，一个 Activity 通常对应一个单独的视图。一个 Android 应用程序是由一个或多个 Activity 组成的，这些 Activity 相当于 Web 应用程序中的网页，用于显示信息，并且相互之间可以进行跳转。和网页跳转不同的是，Activity 之间的跳转可以有返回值。

新打开一个视图时，之前的那个视图会被置为暂停状态，并且压入历史堆栈中，用户可以通过回退操作返回以前打开过的视图。Activity 是由 Android 系统进行维护的，它有自己的生命周期，即“产生、运行、销毁”，但是在这个过程中会调用许多方法，如创建 onCreate()、激活 onStart()、恢复 onResume()、暂停 onPause()、停止 onStop()、销毁 onDestroy()和重启 onRestart() 等。

## Service

Service 是一种类似于 Activity 但是没有视图的程序，它没有用户界面，可以在后台运行很长时间，相当于操作系统中的一个服务。

Android 定义了两种类型的 Service，即本地 Service 和远程 Service。本地 Service 是只能由承载该 Service 的应用程序访问的组件，而远程 Service 是供在设备上运行的其他应用程序远程访问的 Service。

通过 Context.startService(Intent service) 可以启动一个 Service，通过 Context. bindService() 可以绑定一个 Service。

## BroadcastReceiver

BroadcastReceiver 的意思是“广播接收者”，顾名思义，它用来接收来自系统和其他应用程序的广播，并做出回应。

在 Android 系统中，当有特定事件发生时就会产生相应的广播。广播体现在方方面面。例如，当开机过程完成后，系统会产生一条广播，接收到这条广播就能实现开机启动服务的功能；当网络状态改变时，系统会产生一条广播，接收到这条广播就能及时地做出提示和保存数据等操作；当电池电量改变时，系统会产生一条广播，接收到这条广播就能在电量低时告知用户及时保存进度等。

BroadcastReceiver 不能生成 UI，通过 NotificationManager 来通知用户有事件发生，对于用户来说是隐式的。

BroadcastReceiver 的 2 种注册方式：

*   在 AndroidManifest. xml 中进行静态注册；
*   在运行时的代码中使用 Context.registerReceiver() 进行动态注册。

只要注册了 BroadcastReceiver，即使对应的事件广播来临时应用程序并未启动，系统也会自动启动该应用程序对事件进行处理。另外，用户还可以通过 Context.sendBroadcast() 将自己的 Intent 对象广播给其他的应用程序。

## ContentProvider

文件、数据库等数据在 Android 系统内是私有的，仅允许被特定应用程序直接使用。在两个程序之间，数据的交换或共享由 ContentProvider 实现。

ContentProvider 类实现了一组标准方法的接口，从而能够让其他的应用保存或读取 ContentProvider 提供的各种数据类型。

## Intent

Intent 并不是 Android 应用程序四大核心组件之一，但是其重要性无可替代，因此在这里我们做一下简单介绍。

Android 应用程序核心组件中的三大核心组件 —— Activity、Service、BroadcastReceiver。通过消息机制被启动激活，而所使用的消息就是 Intent。Intent 是对即将要进行的操作的抽象描述，承担了 Android 应用程序三大核心组件相互之间的通信功能。