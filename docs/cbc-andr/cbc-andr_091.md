# Android 智能传感器

> 原文：[`c.biancheng.net/android/90/`](http://c.biancheng.net/android/90/)

本章主要讲解了位置服务的相关内容，介绍了和位置服务相关的类和接口的功能。

借助于 Google Map API，可以方便地实现位置服务应用程序。需要注意的是，Google Map API 并不是 Android SDK 自带的包，需要额外配置，其调试程序所使用的 AVD 和之前的不同，需要支持 Google APIs，其主 Activity 必须继承自 MapActivity，而不是 Activity。

通过 MapView 类的对象可以从网络下载 Google Map 数据，供我们自己的应用程序使用，但是使用 MapView 组件必须指定其 apiKey 属性，否则不能正常运行。apiKey 必须与应用程序的签名密钥相匹配，所以本章也介绍了在 Google 网站使用 Debug 密钥申请对应的 apiKey 的方法。

另外，本章的实例中进行定位时，为了便于使用 AVD 调试应用程序，使用的都是 GPS 定位方式。若要使用网络定位方式，则只需要将 LocationManager.GPS_PROVIDER 修改为 LocationManager.NETWORK_PROVIDER 即可，其他代码不需要改变。

本章还对传感器编程进行了简单的介绍。Android 平台支持多种传感器，通过传感器可以获取数据，各种传感器的编程方式基本相同，易于读者掌握。活用传感器可以开发出极具感染力的应用程序，传感器已被越来越多地应用于 Android 系统中手机游戏的开发。