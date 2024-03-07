# Google Map API 开发简介

> 原文：[`c.biancheng.net/view/3227.html`](http://c.biancheng.net/view/3227.html)

Google Map 是 Google 公司提供的电子地图服务，它能提供三种视图，分别是矢量地图（传统地图）、俯视地图、地形地图。

*   矢量地图可以提供政区、交通以及商业信息。
*   俯视地图可以提供不同分辨率的卫星照片。
*   地形地图可以用于显示地形和等高线。

2006 年，Google 发布了一个基于 Java 的应用程序 Google Maps for Mobile，可以用在 Java-Based 的手机上，支持 Symbian、Windows Mobile 等移动平台。在 Android 系统推出后，Google Map 服务得到了更好的发展。

2010 年 11 月 30 日，Google 宣布，正式推出最新版地图服务 Google Earth 6，新版整合了街景和 3D 技术，可为用户提供逼真的浏览体验。新版本支持 Windows、OS X 和 Linux 操作系统。

近期，Android 版本的 Google Map 服务在其基本服务的基础上又追加了集成 Google Offers 服务，对 Google Business Photos（企业照片）服务提供支持，支持室内步行导航、自动搜寻附近餐厅、酒吧、加油站等详细资料的功能。

Google Map API 是 Google 自己推出的编程 API，可以让全世界对 Google Map 有兴趣的程序设计人员自行开发基于 Google Map 的服务，建立自己的地图应用程序。

通过 Google Map 为开发者提供的地图 API 可以开发出各种各样有趣的地图 Mash-Up 应用，还可以将不同地图图层加载到应用中，如卫星影像、根据海拔高度绘制的高山和植被地形图、街道视图等，从而帮助开发者打造个性化的地图应用站点。

Google 地图 API 是一种通过 JavaScript 将 Google 地图嵌入网页的 API。该 API 提供了大量实用工具用以处理地图，并通过各种服务向地图添加内容，从而使用户能够在网站上创建功能全面的地图应用程序。

Google Map API 定义了一系列用于在 Google Map 上显示、控制和层叠信息的功能类，放置在名为 com.google.android.maps 的包中。

Google Map API 不属于 Android SDK 的标准库组件，需要单独下载。

下面介绍几个常用的类和接口。

**1）MapView**

用于显示地图的 View 组件，它派生自 android.view.ViewGroup。使用这个组件的时候必须和 MapActivity 配合使用，MapView 需要由 MapActivity 来管理。

MapView 提供了 3 种模式的地图，分别为交通模式、卫星模式、街景模式，分别可以通过以下方式设置采用什么模式来显示地图，代码如下：

```

MapView map=new MapView(this,"Android Maps API Key");//得到 MapView 对象
map.setTraffic(true); //设置为交通模式
map.setSatellite(true); //设置为卫星模式
map.setStreetView(true); //设置为街景模式
```

**2）MapActivity**

用于显示 Google Map 的 Activity 类，它是一个抽象类，任何想要显示 MapView 的 Activity 都需要派生自 MapActivity，并且在其派生类的 onCreate() 中要创建一个 MapView 实例。

**3）MapController**

用于控制地图的移动、缩放等操作。可以通过以下代码获得 MapController：

MapController  mapController=map.getController();

**4）Overlay**

用于显示地图上可绘制的对象，例如显示地点图标。

**5）GeoPoint**

包含经纬度位置的对象，它可以实现地点定位。要想实现一个简单的地点定位，首先需要构建一个 GeoPoint 来表示地点的经度和纬度，然后使用 animateTo 方法将地图定位到 GeoPoint 代表的经纬度的地点，例如：

GeoPoint geoPonit=new GeoPoint((int)(12.5*1000000),(int)(122.6*1000000));
mapController.animateTo(geoPonit);