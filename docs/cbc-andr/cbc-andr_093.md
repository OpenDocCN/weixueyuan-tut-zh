# Android LBS 位置服务开发简介

> 原文：[`c.biancheng.net/view/3223.html`](http://c.biancheng.net/view/3223.html)

手机设备的移动性决定了手机在位置服务方面拥有比固定设备更多的优势，可以开发多种基于移动设备的位置服务应用程序。

Android SDK 提供了 android.location 包和 Google Maps API 支持位置服务功能，开发人员可以方便地开发自己的位置服务应用程序。

Android 系统支持 GPS 定位方式和网络定位方式。GPS 方式的位置信息来自卫星，精度很高，但是 GPS 方式仅在户外有效，其首次获取位置时间较长并且非常耗费电量。

而网络定位方式使用的是移动通信基站和 WIFI 信号，这种方式在室内和户外都可以使用，响应快速，费电较少，但是精度难以保证。开发者应该根据应用程序的使用环境来确定具体的定位方式。

## LocationManager 介绍

在 Android 的位置服务中，LocationManager 是一个非常重要的类，它位于 android.location 包中。

LocationManager 类用于管理 Android 用户位置服务信息，提供确定用户位置的 API，通过这个类可以实现定位、跟踪和目标趋近等功能。

LocationManager 对象不能直接实例化，可以通过 Context.getSystemService(Context. LOCATION_SERVICE) 方法获得。

LocationManager 对象可以完成以下三个方面的任务：

*   从用户的位置查询所有可用的 LocationProvider 列表。
*   从特定的 LocationProvider 周期性获取用户当前位置的功能。
*   当用户位置接近某个特定区域时，启动相关任务。

表 1 所示为 LocationManager 类的常用方法。

**表 1 LocationManager 类的常用方法**

| 常用方法 | 方法描述 |
| --- | --- |
| getAllProviders() | 获得所有 LocationProvider 列表 |
| getBestProvider (Criteria criteria,Boolean enabldOny)  | 根据 criteria 返回最合适的 LocationProvider，其中 criteria 指定了一系列条件 |
| getLastKnownLocation (String provider)  | 根据 provider 获得最新位置信息 |
| getProvider (String name)  | 获得指定名称的 LocationProvider |
| getProviders (boolean enabledOnly) | 获得可用的 LocationProvider 列表 |
| ddProximityAlert (double latitude, double longitude, float radius, long expiration, PendingIntent intent) | 设定目标趋近警告 |
| removeProximityAlert (PendingIntent intent) | 删除趋近警告 |

## LocationProvider 介绍

LocationProvider 为位置提供者的抽象类，位置提供者提供手机设备周期性的地理位置报告。

LocationProvider 的常用方法如表 2 所示。

**表 2 LocationProvider 的常用方法**

| 常用方法 | 方法描述 |
| --- | --- |
| getAccuracy() | 获取 LocationProvider 提供位置信息的精度 |
| getName() | 获得 LocationProvider 的名称 |
| getPowerRequirement() | 获得 LocationProvider 对电源的需求 |
| hasMonetaryCost() | 获取当前 LocationProvider 是免费的还是收费的 |
| meetsCriteria (Criteria criteria) | 确定当前 LocationProvider 是否符合特定条件 |
| requiresCell()  | LocationProvider 定位是否需要访问基站网络 |
| requiresNetwork() | LocationProvider 定位是否需要 Internet 网络数据 |
| requiresSatellite() | LocationProvider 定位是否需要获取卫星数据 |
| supportsAltitude() | LocationProvider 提供的位置信息是否包含高度信息 |
| supportsBearing() | 是否能够提供方向信息 |
| supportsAltitude() | LocationProvider 是否能够提供速度信息 |

根据设备的不同可以使用不同的定位技术来实现位置服务，也就是获取不同的 LocationProvider，以下是 3 种获取 LocationProvider 实例的方法。

**1）通过指定名称获取**

根据 LocationManager 中的静态常量 GPS_PROVIDER 和 NETWORK_PROVIDER 来分别获得 GPS Provider 和 Network Provider，例如：

```

LocationManager manager=(LocationManager)getSystemService
(Context.LOCATION_SERVICE);
//指定 Provider 名称为 LocationManager.GPS_PROVIDER
LocationProvider MyProvider=manager.getProvider(LocationManager.GPS_PROVIDER);
```

**2）获取可使用的 LocationProvider 实例列表**

通过调用 locationManager.getProviders(true) 方法就可以获得可利用的 LocationProvider 实例列表，例如：

```

342oolean enabledOnly=true;
//获取 Provider 的集合
List<String>providers=locationManager.getProviders(enabledOnly);
```

**3）根据 Criteria 获取符合条件的 LocationProvider 实例**

每个 LocationProvider 都有一个条件集合，以便于应用程序可以选择合适的位置提供者。

例如有的 LocationProvider 要求设备本身具有 GPS 模块，并且要求看见卫星的数量，有的要求手机设备能够接入 Internet 或者特定网络等。Criteria 类被用于为 LocationProvider 设置相关条件。

Criteria 对象封装了获得 LocationProvider 实例的条件，可以根据指定的 Criteria 条件来过滤 LocationProvider 列表，以得到符合条件的 LocationProvider 实例。

Criteria 的常用方法如表 3 所示。

**表 3 ****Criteria 的常用方法**

| 常用方法 | 方法描述 |
| --- | --- |
| isAltitudeRequired() | 返回 Provider 是否需要高度信息 |
| isBearingRequired() | 返回 Provider 是否需要方向信息 |
| isSpeedRequired() | 返回 Provider 是否需要速度信息 |
| isCostAllowed() | 标识是否允许产生费用 |
| setAccuracy (int accuracy) | 设置 Provider 的精确度 |
| setAltitudeRequired (boolean altitudeRequired) | 设置 Provider 是否需要高度信息 |
| setBearingRequired (boolean bearingRequired) | 设置 Provider 是否需要方位信息 |
| setSpeedRequired (boolean speedRequired) | 设置 Provider 是否需要速度信息 |
| setCostAllowed (boolean cost Allowed)  | 设置是否需要产生费用 |
| getAccuracy() | 获取位置信息的准确度 |

下面的代码使用 Criteria 对象创建了一个条件集，并根据该条件集查找到合适的 LocationProvider 对象，并从该 LocationProvider 对象获取位置信息。

```

//获取 Criteria 对象
Criteria criteria=new Criteria();
//设置需要更精细的定位精度要求
criteria.setAccuracy (Criteria.ACCURACY_FINE);
//设置定位不需要高度信息
criteria.setAltitudeRequired (false);
//设置不需要提供轴承信息
criteria.setBearingRequired (false);
//设置允许收费
criteria.setCostAllowed (true);
//设置低耗能
criteria.setPowerRequirement (Criteria.POWER_LOW);
//根据设置的条件获取合适的 provider
String provider=locationManager.getBestProvider (criteria,true);
//得到 Location 对象
Location location=locationManager.getLastKnownLocation (provider);
```

**4）LocationListener，提供定位信息发生改变时的回调功能。**

此接口提供了 4 个常用方法，如表 4 所示。

**表 4 LocationListener 常用方法**

| 常用方法 | 方法描述 |
| --- | --- |
| onLocationChanged (Location location) | 当坐标改变时触发此方法，如果 Provider 传进相同的坐标，他就不会被触发。 |
| onProviderDisabled (String provider) | Provider 禁用时触发此方法,例如，GPS 被关闭时 |
| onProviderEnabled (String provider) | Provider 启用时触发此方法,例如，GPS 被打开时 |
| onStatusChanged (String provider,int status,Bundle extras) | Provider 的状态可用 AVAILABLE 暂时不可用 TEMPORARILY_UNAVAILABLE 和无服务 OUT_OF_SERVICE 三个状态之间切换时触发此函数。 |