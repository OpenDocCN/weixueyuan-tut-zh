# Android USB 主机模式开发教程

> 原文：[`c.biancheng.net/view/3215.html`](http://c.biancheng.net/view/3215.html)

当 Android 设备运行在 USB 主机模式，它就像一个真正的 USB 主机，给 USB 总线供电并枚举所有连接的 USB 附件设备。

USB 主机模式仅在 Android 3.1 及其更高版本的系统中被支持。

#### 1．相关 API 介绍

与 USB 主机相关的 API 都被保存在 android.hardware.usb 包中，相关类介绍如下表所示。

| 名称 | 作用 |
| --- | --- |
| UsbManager | 用于枚举连接的 USB 附件设备，并与 USB 附件进行通信。 |
| UsbDevice | 表示一个连接的 USB 附件设备，包含访问该设备的标识信息、接口和端点的相关方法。 |
| UsbInterface | 表示 USB 设备的一个接口，用于定义 USB 设备的一系列功能。一个设备可以有一个或者多个接口，这些接口可用于通信。 |
| UsbEndpoint | 表示一个接口的端点，相当于接口通信的通道。一个接口可以拥有一个或者多个端点，通常拥有输入和输出两个端点用于设备的双向通信。 |
| UsbDeviceConnection | 表示一个到 USB 设备的连接，用于在端点上传输数据。这个类允许用户以同步或者非同步的方式双向传输数据。 |
| UsbRequest | 表示一个通过 UsbDeviceConnection 与 USB 设备进行通信的异步请求。 |
| UsbConstants | 定义了一些 USB 常量，这些常量与 Linux 内核中 linux/usb/ch9.h 的定义相同。 |

在大多数情况下，开发者需要使用上面提到的所有的类来与 USB 设备连接（UsbRequest 类只有在要求异步通信时才会被使用）。

通常情况下，开发者需要通过 UsbManager 实例去获取所需的 UsbDevice 实例，进而从 UsbDevice 实例中查找合适的 UsbInterface，并确定要用于通信的 UsbEndpoint，最后建立 UsbDeviceConnection 与 USB 设备的通信。

#### 2．Android Manifest 文件配置

由于并不是所有的 Android 设备都支持 USB 附件 API，因此需要在应用程序的 Manifest 文件中使用 <uses-feature> 属性来声明当前的应用程序使用了 android. hardware.usb.host 特性。

设置最小 SDK 版本，该值应该大于 12，因为在早期的 Android SDK 版本中不支持 USB 主机模式。

如果需要当前应用程序在 USB 附件连接时获得通知，则应该在主 Activity 中为 android.hardware.usb.action.USB_ACCESSORY_ATTACHED Intent 指定 <intent-filter> 属性和 <meta-data> 属性对。其中，<meta-data> 属性指向一个外部的 XML 资源文件，其中包含要检测的附件的识别信息。

在 XML 资源文件中，通过 <usb-device> 属性为要过滤的 USB 附件定义相关信息，该属性可以包含 vendor-id、product-id、class、subclass、protocol（device or interface）等属性。

*   如果想过滤特定设备，则需要使用 vendor-id 和 product-id 属性；
*   如果想过滤一类设备，比如大容量存储设备或者数码相机，则应该使用 class、subclass 和 protocol 属性；
*   如果这些属性一个也不指定，则所有的 USB 设备都不会被过滤掉。
*   该资源文件被保存在 res/xml 文件夹下，其文件名字应该和上面提到的 <meta-data> 中指定的文件名字相同（不含 .xml 后缀）。

下面的示例代码演示如何定义 Manifest 文件：

```

<?xml version="1.0" encoding="utf-8"?>
<manifest ...>
    <uses-feature android:name="android.hardware.usb.host" />
    <uses-sdk android:minSdkVersion="12" />
    ...
    <application>
        <activity ...>
            ...
            <intent-filter>
                <action android:name="android.hardware.usb.action.USB ACCESSORY ATTACHED"/>
            </intent-filter>

            <meta-data
                android:name="android.hardware.usb.action.USB_ACCESSORY_ATTACHED"
                android:resource="@xml/device_filter" />
        </activity>
    </application>
</manifest>
```

在这个示例中，自由文件被放置在 res/xml/device_filter.xml 中，相关代码如下：

```

<?xml version="1.0" encoding="utf-8"?>
<resources>
    <usb-device vendor-id="1234" product-id="5678" class="255" subclass="66" protocol="1"/>
</resources>
```

#### 3．使用 USB 设备

当用户将 USB 设备连接到 Android 设备时，Android 系统会检测相关应用程序是否对连接的 USB 设备感兴趣。如果感兴趣，则会建立与 USB 设备的通信。要达到这个目的，应用程序应该能够完成以下三点。

*   通过 Intent Filter 发现连接的 USB 设备，可以通过过滤设备连接事件或者枚举所有连接的 USB 设备并从中查找合适的设备的方式对设备进行发现。
*   向用户要求与 USB 设备进行通信的权限。
*   通过使用合适的接口读写数据的方式与 USB 设备进行通信。

**1）使用 Intent Filter 发现设备的方式适合想让应用程序自动检测设备的情况。**

实行这种方式需要在应用程序的 manifest 文件中为 Activity 添加 android.hardware.usb.action.USB_ ACCESSORY_ATTACHED Intent 的过滤功能，并通过 meta-data 属性指定对 USB 设备信息进行描述的 XML 文件。

Activity 设置的相关代码如下：

```

<activity ...>
    ...
    <intent-filter>
        <action android:name="android.hardware.usb.action.USB ACCESSORY ATTACHED"/>
    </intent-filter>

    <meta-data
        android:name="android.hardware.usb.action.USB_ACCESSORY_ATTACHED"
        android:resource="@xml/device_filter" />
</activity>
```

资源文件 device_filter.xml 的内容如下：

```

<?xml version="1.0" encoding="utf-8"?>
<resources>
    <usb-device vendor-id="1234" product-id="5678"/>
</resources>
```

这样，当符合要求的 USB 设备被连接到 USB 主机上时，其产生的 Intent 对象就会被该 Activity 截获，并从中获取到代表 USB 设备的 UsbAccessory 对象。

UsbAccessory accessory=(UsbAccessory) intent.getParcelableExtra(UsbManager.EXTRA_DEVICE);

**2）枚举所有连接的 USB 设备。获取所有连接到主机的 USB 设备的代码如下：**

```

UsbManager manager=(UsbManager)getSystemService(Context.USB_SERVICE);
...
HashMap<String,UsbDevice>deviceList=manager.getDeviceList();
UsbDevice device=deviceList.get("deviceName");
```

如果需要，也可以从哈希表中获取迭代器，对每一个设备分别进行处理：

```

UsbManager manager=(UsbManager)getSystemService(Context.USB_SERVICE);
HashMap<String,UsbDevice>deviceList=manager.getDeviceList();
Iterator<UsbDevice> deviceIterator = deviceList.values().iterator();
while(deviceIterator.hasNext()){
    UsbDevice device = deviceIterator.next();
    //your code
}
```

**3）获取访问 USB 设备的权限。**

在与 USB 设备建立通信之前，必须明确要向用户要求访问权限。通过调用 requestPermission() 方法向用户显示一个对话框，要求与设备建立连接的权限。

用户单击该对话框后会生成一个 Intent 对象并广播出去。因此，该应用程序需要创建一个 BroadcastReceiver 来接收该 Intent 对象，并从中获取用户授权。

创建 BroadcastReceiver 的相关示例代码如下：

```

private static final String ACTION_USB_PERMISSION=
"com.android.example.USB_PERMISSION";

private final BroadcastReceiver mUsbReceiver=new BroadcastReceiver(){
    public void onReceive (Context context, Intent intent) {
        String action=intent.getAction (); if (ACTION_USB_PERMISSION.equals (action)) { synchronized (this) {
            UsbDevice device= (UsbDevice) intent.getParcelableExtra (UsbManager.EXTRA_DEVICE);
            if (intent.getBooleanExtra (UsbManager.EXTRA_PERMISSION_GRANTED, false)){
                if (device !=null) {
                //call method to set up device communication
                }
            }
            else {
                Log.d (TAG, "permission denied for device "+device);
            }
        }
    }
}
```

该 BroadcastReceiver 被创建后，应该在 Activity 的 onCreate() 方法中进行注册。相关代码如下：

```

UsbManager mUsbManager= (UsbManager) getSystemService (Context.USB_SERVICE);
private static final String ACTION_USB_PERMISSION=
"com.android.example.USB_PERMISSION";
...
mPermissionIntent=PendingIntent.getBroadcast (this, 0, new Intent (ACTION_USB_PERMISSION),0);
IntentFilter filter=new IntentFilter(ACTION_USB_PERMISSION);
registerReceiver (mUsbReceiver, filter);
```

显示向用户要求访问设备权限的对话框，需要使用 requestPermission() 方法，代码如下：

```

UsbAccessory accessory;
...
mUsbManager.requestPermission(accessory, mPermissionIntent);
```

**4）与 USB 设备进行通信。**

与 USB 设备进行通信可以异步，也可以同步。无论在哪种情况下，与 USB 设备通信的过程都应该在一个单独的线程里被执行，以避免阻塞 UI 线程。

为了合理地创建与 USB 设备的通信，开发者需要获取要通信设备的适合的 UsbInterface 和 UsbEndpoint 对象，并在该端点上建立 UsbDeviceConnection 并发送通信请求。

总体来说，代码编写应该完成以下功能：

*   检查 UsbDevice 对象的属性，例如 product-id、vendor-id 等，以确认是否要和当前设备进行通信。
*   获取合适的 UsbInterface 和 UsbEndpoint 对象用于通信。
*   通过 UsbEndpoint 对象打开 UsbDeviceConnection。
*   在端点上使用 bulkTransfer() orcontrolTransfer()传输数据。该过程应该在单独的线程中进行。

下面的代码演示了使用同步方式进行数据传输的过程，该示例仅用于演示，在真正的开发过程中，应该注意选择合适的接口和端点，并且在单独的线程中进行数据传输。

```

private Byte[] bytes
private static int TIMEOUT=0;
private boolean forceClaim=true;

...
UsbInterface intf=device.getInterface (0);
UsbEndpoint endpoint=intf.getEndpoint (0);
UsbDeviceConnection connection=mUsbManager.openDevice (device);
connection.claimInterface (intf, forceClaim);
connection.bulkTransfer (endpoint, bytes, bytes.length, TIMEOUT); //do in another thread
```

进行异步数据传输使用 UsbRequest 类来初始化并将一个异步请求放入请求队列，然后调用 requestWait() 方法等待结果。

#### 5）结束与 USB 设备的通信。

当与 USB 设备的通信结束或者设备被从系统中断开的时候，应该通过调用 releaseInterface() 方法和 close() 方法关闭 UsbInterface 和 UsbDeviceConnection。

下面的代码定义了一个监听设备被移除的事件的 BroadcastReceiver：

```

BroadcastReceiver mUsbReceiver=new BroadcastReceiver(){
    public void onReceive (Context context, Intent intent) {
        String action=intent.getAction ();
        if (UsbManager.ACTION_USB_ACCESSORY_DETACHED.equals (action)) {
            UsbAccessory accessory= (UsbAccessory) intent.getParcelableExtra (UsbManager.EXTRA_DEVICE);
            if (accessory !=null) {
                // call your method that cleans up and closes communication with the device
            }
        }
    }
};
```

在应用程序中创建该 BroadcastReceiver，而不是在应用程序的 Manifest 配置文件中，这样可以保证只有 Activity 运行时才对 USB 设备断开事件进行处理。而设备断开事件只会被发送给当前运行的 Activity，而不是被广播给所有的应用程序。