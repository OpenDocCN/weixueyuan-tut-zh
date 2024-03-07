# Android 硬件加速开发简介

> 原文：[`c.biancheng.net/view/3282.html`](http://c.biancheng.net/view/3282.html)

从 Android 3.0（API Level 11）开始，Android 2D 渲染管线被设计为能更好地支持硬件加速功能。

硬件加速功能将所有在 View 组件的 Canvas 上执行的绘制操作都交由 GPU 来完成。由于硬件加速功能需要更多的资源，因此启用硬件加速功能的应用程序会耗费更多的内存资源。

## 启用硬件加速

启用硬件加速最简单的方法是在总体上为整个应用程序打开硬件加速功能。如果应用程序中仅仅使用了标准的 View 和 Drawable 对象进行图像绘制，那么在总体上打开硬件加速功能不会出现任何不良影响。

但是，由于硬件加速功能并不是被所有的 2D 绘制操作所支持的，因此对于一些自定义的 View 组件和 Drawable 对象的绘制，可能会出现无法显示、异常或者错误渲染的点等问题。为了避免这类问题的发生，Android 平台提供了以下 4 个应用层次的硬件加速开关设置。

*   Application，应用程序级。
*   Activity，视图级。
*   Window，窗口级。
*   View，组件级。

若在 Application 等级打开硬件加速功能，则整个应用程序中所有的绘图工作都使用硬件加速。打开方法是在应用程序的配置文件 AndroidManifest.xml 的 <application> 中添加如下代码：

<application android:hardwareAccelerated="true" ...>

若在整个应用程序等级下使用硬件加速功能导致了某些问题，则可以针对某个 Activity 具体设置是否打开硬件加速功能。

以下代码表示在应用程序等级启用硬件加速功能，但是对某个 Activity 不使用硬件加速功能：

```

<application android:hardwareAccelerated="true">
    <activity ... />
    <activity android:hardwareAccelerated="false" />
</application>
```

如果需要更细粒度的控制，可以通过以下代码使某个窗口获得硬件加速功能。

getWindow().setFlags(
    WindowManager.LayoutParams.FLAG_HARDWARE_ACCELERATED,
    WindowManager.LayoutParams.FLAG_HARDWARE_ACCELERATED);

就目前的 API 控制来讲，仅支持打开某个窗口的硬件加速功能，而不支持关闭某个窗口的硬件加速功能。

在 View 等级，可以在运行时关闭某个 View 组件的硬件加速功能，但是在这个等级，只能关闭硬件加速功能，不能启用硬件加速功能。

在某些情况下，能够知道当前应用程序或者某个自定义 View 是否被正确地硬件加速是很有用的，尤其是当不是所有的自定义绘图操作都被渲染管线很好地支持的时候。

有两种方式可以确认当前应用程序是否被硬件加速。

*   View.isHardwareAccelerated()：当 View 被附加到硬件加速的窗口时，返回 true。
*   Canvas.isHardwareAccelerated()：当 Canvas 被硬件加速时，返回 true。

如果必须要做这样的检查，尽量使用 Canvas.isHardwareAccelerated() 方法代替 View.isHardwareAccelerated() 方法。

因为当 View 被附加到硬件加速的窗口时，它仍有可能使用非硬件加速的 Canvas 进行绘制。这种情况在因为缓存原因将 View 绘制到位图中时经常发生。

## Android 绘图模型

#### 1．基于软件的绘图模型

当应用程序需要更新 UI 的某一个部分时，会通过更改内容的 View 组件调用 invalidate() 方法将当前组件无效化。

该方法触发一个重绘消息，该消息会沿着视图的层次一直向上传递，以计算需要重绘的区域。然后 Android 系统会重绘在视图层次中与要重绘区域有交叉的所有组件。

基于软件的绘图模式主要完成如下两个工作：

*   无效化绘图层次。
*   重绘绘图层次。

这种绘图模型有以下两个缺点。

**1）这个模型会导致在消息传递过程中多执行大量无效的绘图代码。**

例如，一个按钮位于一个 View 上，当该按钮被单击时，虽然该 View 没有也会被重绘。

**2）这种绘图模型可能隐藏应用程序中的 Bug。**

由于 Android 系统会重绘所有与需重绘区域有交叉的 View 组件，一个被用户改变了内容的 View 组件可能会被重绘，即使该组件没有调用 invalidate() 方法。

当这种情况发生的时候，用户只能依靠另一个组件的重绘操作来获取自己想要的效果，而这种效果可能会不断改变。因此，开发者应该在自定义的组件上不断调用 invalidate() 方法以保证内容显示正确，无论该组件的内容是否被改变。

当 Android 组件的内容发生改变，如背景色改变或者文本改变时，该组件会自动调用 invalidate() 方法。

#### 2．硬件加速绘制模型

在硬件加速绘制模型下，Android 系统依然使用 invalidate() 方法和 draw() 方法来对屏幕进行更新并绘制图形，但是具体处理的方法有所不同。

这种模式下，Android 系统并没有马上执行绘图命令，而是记录了当前视图的显示列表。显示列表中包含视图层次中所有绘图代码的输出。

Android 系统只需要录制并且更新需要重绘组件的显示列表即可。那些没有被无效化的组件可以简单通过重新使用之前记录的显示列表的方式来重绘图形。

硬件加速绘图模型主要完成如下三个工作：

*   无效化视图的绘图层次。
*   记录并更新显示列表。
*   绘制显示列表。

在这种模式下，不是通过需要更新的组件的 draw() 方法来更新图像，而是应该调用 invalidate() 方法来使 Android 系统记录组件的显示列表。如果没有这样做，该组件的更新将不会显示出来。

使用显示列表方式绘制图像对动画绘制也有很大好处。因为设置特定属性，例如透明度、旋转灯，不需要重新绘制整个视图，而只需对特定属性进行更改即可。

例如，假如有一个 LinearLayout，该 LinearLayout 中包含一个 ListView 组件和一个 Button 组件，ListView 组件被放置在 Button 组件的上面。该 LinearLayout 组件的显示列表如下：

*   DrawDisplayList(ListeView)
*   DrawDisplayList(Button)

如果开发者需要更改 ListView 的透明度，那么通过 ListView 对象调用 setAlpha(0.5f) 方法后，LinearLayout 的显示列表如下：

*   SaveLayerAlpha(0.5)
*   DrawDisplayList(ListeView)
*   Restore
*   DrawDisplayList(Button)

由此可见，绘图 ListView 的复杂代码并没有被执行，系统只是简单更新了 LinearLayout 的显示列表。对于一个没有被硬件加速的应用程序，该过程中的每一行代码都会被重新执行一次。