# Android 应用程序发布的步骤

> 原文：[`c.biancheng.net/view/3478.html`](http://c.biancheng.net/view/3478.html)

一个 Android 应用程序从开发到发布的过程一般要经过下列步骤。

**1）**完成开发工作，在模拟器上测试运行。

**2）**将应用程序开发过程中的调试信息移除。

**3）**考虑为应用程序添加 EULA（ End User License Agreement）。

**4）**为应用程序添加自己的图标，取代默认的 Android 图标。

**5）**定义应用程序的版本。

**6）**为应用程序进行签名。

**7）**如果应用程序使用了 Google Map API，需要申请 Map API 密钥。

**8）**在真机上测试运行。

**9）**测试完成后，发布到 Google Play Store 或者其他应用程序网站。

其中，应用程序的版本由 AndroidManifest.xml 文件中的 <Manifest> 标签指定，例如：

```

<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="introduction.android.gpsLocationInMapDemo"
    android:versionCode="1"
    android:versionName="1.0">
    ...
</ manifest>
```

其中，android:versionCode 为一个整数值，代表应用程序代码的相对版本，也就是版本更新过多少次。该值每次更新的值都应该比前一次大，以便于检查应用程序是否需要升级，此处表示第一次更新。

android:versionName 为一个字符串值，代表应用程序的版本信息，需要显示给用户，此处表示 1.0 版本。