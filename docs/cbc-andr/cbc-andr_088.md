# Android USB 附件模式开发教程

> 原文：[`c.biancheng.net/view/3209.html`](http://c.biancheng.net/view/3209.html)

Android USB 附件模式允许 Android 设备以附件形式连接到 USB 主机上，附件必须遵循 Android Accessory Protocol 协议。

附件模式使得不能以 USB 主机方式工作的 Android 设备与 USB 主机进行交互。

**1．API 的选择**

在开发 USB 附件应用程序时，首先应该考虑的问题是选择正确的 USB 附件 API。

对应 Android 3.1（API Level 12）及其以上版本的操作系统，Android SDK 直接提供了 USB 附件开发包，名为 android.hardware.usb。

对于早期的 Android 2.3.4（API Level 10）版本的操作系统，Android SDK 没有提供相应的开发包，只能通过 Google 的附加库来完成 USB 附件模式的相关开发工作，包名为 com.android.future.usb。

实质上，Google 的这个附加库是对 Android 框架 API 的包装，相当于 android.hardware.usb 包的一个简化包，仅支持附件模式的开发，不支持主机模式的开发。

由于 Google 附件库是 Android 框架 API 的包装库，因此支持 USB 附件的 API 是相同的，主要有以下两个类。

*   UsbManager：负责枚举连接的 USB 附件设备，并与附件通信。
*   UsbAccessory：表示一个 USB 附件设备，并且包含访问附件的标识信息的 API。

虽然 USB 附件开发的相关的类相同，但是在使用方法上有两处不同。

**1）获取 UsbManager 实例的方法不同。**

对于 Google 附加库，应使用如下代码获取 UsbManager 的实例：

UsbManager manager=UsbManager.getInstance(this);

对于 Android 框架 API，应该使用如下代码：

UsbManager manager=(UsbManager)getSystemService(Context.USB_SERVICE);

**2）当通过 Intent Filter 对连接的 USB 附件进行过滤时，代码有所不同。此时，UsbAccessory 对象会被包含在 Intent 对象中传递给应用程序。**

对于 Google 附加库，应该使用如下代码：

UsbAccessory accessory=UsbManager.getAccessory(intent);

如果使用的是 Android 框架 API，则应该使用如下代码：

UsbAccessory accessory=(UsbAccessory)intent.getParcelableExtra(UsbManager.EXTRA_ACCESSORY);

**2．AndroidManifest.xml 文件设置**

应用程序的 Manifest 文件应该做如下设置：

1）由于并不是所有的 Android 设备都支持 USB 附件 API，因此需要在应用程序的 Manifest 文件中使用 <uses-feature> 属性来声明当前的应用程序使用了 android. hardware.usb.accessory 特性。

2）如果使用的是 Google 附加库，需要使用 <uses-library> 属性添加 com.android.future.usb.accessory 库的支持。

3）根据选用的 API 设置最小 SDK 版本，如果是 Google 附加库，最小 API Level 应该为 10，如果使用的是框架 API，则该值应该为 12。

4）如果需要当前应用程序在 USB 附件连接时获得通知，则应该在主 Activity 中为 android.hardware.usb.action.USB_ACCESSORY_ATTACHED intent 指定 <intent-filter> 属性和 <meta-data> 属性对。其中，<meta-data> 属性指向一个外部的 XML 资源文件，其中包含要检测的附件的识别信息。

5）在 XML 资源文件中，通过 <usb-accessory> 属性为要过滤的 USB 附件定义声明信息，该属性可以包含 manufacture、model 和 version 属性。该资源文件被保存在 res/xml 文件夹下，其文件名字应该和上面提到的 <meta-data> 中指定的文件名字相同（不含 .xml 后缀）。

下面的代码演示了在应用程序的 AndroidManifest.xml 文件中，关于 USB 附件 API 的设置内容：

```

<?xml version="1.0" encoding="utf-8"?>
<manifest ...>
    <uses-feature android:name="android.hardware.usb.accessory" />

    <uses-sdk android:minSdkVersion="<version>" />
    ...
    <application>
    <uses-library android:name="com.android.future.usb.accessory" />
        <activity ...>
            <intent-filter>
                <action android:name="android.hardware.usb.action.USB ACCESSORY ATTACHED"
            </intent-filter>

            <meta-data
                android:name="android.hardware.usb.action.USB_ACCESSORY_ATTACHED"
                android:resource="@xml/accessory_filter" />
        </activity>
    </application>
</manifest>
```

其中：

<meta-data   
    android:name="android.hardware.usb.action.USB_ACCESSORY_ATTACHED"
    android:resource="@xml/accessory_filter" />

表明 USB 附件的相关信息，包括附件模型、生产商、版本等信息，被保存在 res/xml/xml/accessory_filter.xml 文件中。当 USB 附件连接到 Android 主机时，这些信息都会被发送给应用程序进行过滤。该文件示例代码如下：

```

<?xml version="1.0" encoding="utf-8"?>
<resources>
    <usb-accessory model="DemoKit" manufacturer="Google" version="1.0"/>
</resources>
```

**3．使用 USB 附件**

当用户将 USB 附件连接到 Android 设备时，Android 系统会检测相关应用程序是否对连接的 USB 附件感兴趣。如果感兴趣，则会建立对 USB 附件的通信。要达到这个目的，应用程序应该能够完成以下三点：

*   通过 Intent Filter 发现连接的 USB 附件设备，可以通过过滤附件连接事件或者枚举所有连接的 USB 附件设备并从中查找合适的设备的方式对附件进行发现。
*   向用户要求与 USB 附件进行通信的权限。
*   通过使用合适的接口读写数据的方式与 USB 附件进行通信。

**1）使用 Intent Filter 发现附件的方式适合想让应用程序自动检测附件的情况。**

实行这种方式需要在应用程序的 manifest 文件中为 Activity 添加 android.hardware.usb.action.USB_ACCESSORY_ATTACHED Intent 的过滤功能，并通过 meta-data 属性指定对 USB 附件信息进行描述的 XML 文件。

Activity 设置的相关代码如下：

```

<activity ...>
    ...
    <intent-filter>
        <action android:name="android.hardware.usb.action.USB_ACCESSORY_ATTACHED" >
    </intent-filter>

    <meta-data android:name="android.hardware.usb.action.USB_ACCESSORY_ATTACHED" android:resource="@xml/accessory_filter" />
</activity>
```

资源文件 accessory_filter.xml 的内容如下：

```

<?xml version="1.0" encoding="utf-8"?>
<resources>
    <usb-accessory manufacturer="Google, Inc." model="DemoKit" version="l.0" />
</resources>
```

这样，当符合要求的 USB 附件被连接到 USB 主机上时，其产生的 Intent 对象就会被该 Activity 截获，并从中获取代表 USB 附件的 UsbAccessory 对象。

对于 Google 附加库，相关代码如下：

UsbAccessory accessory=UsbManager.getAccessory(intent);

对于框架 API，相关代码如下：

UsbAccessory accessory=(UsbAccessory)intent.getParcelableExtra(UsbManager.EXTRA_ACCESSORY);

**2）枚举所有连接的 USB 附件。**

获取所有连接到主机的 USB 附件的代码如下：

UsbManager manager=(UsbManager)getSystemService(Context.USB_SERVICE);
UsbAccessory[] accessoryList=manager.getAcccessoryList();

**3）获取访问 USB 附件的权限。在与 USB 附件建立通信之前，必须明确要向用户要求访问权限。**

通过调用 requestPermission() 方法向用户显示一个对话框，要求与附件建立连接的权限。用户单击该对话框后会生成一个 Intent 对象并广播出去。因此，该应用程序需要创建一个 BroadcastReceiver 来接收该 Intent 对象，并从中获取用户授权。

创建 BroadcastReceiver 的相关示例代码如下：

```

private static final String ACTION_USB_PERMISSION =
            "com.android.example.USB_PERMISSION";
private final BroadcastReceiver mUsbReceiver = new BroadcastReceiver() {
    public void onReceive(Context context, Intent intent) {
        String action = intent.getAction();
        if (ACTION_USB_PERMISSION.equals(action)) {
            synchronized (this) {
                UsbAccessory accessory = (UsbAccessory) intent.getParcelableExtra(UsbManager.EXTRA_ACCESSORY);
                if (intent.getBooleanExtra(UsbManager.EXTRA_PERMISSION_GRANTED, false)) {
                    if (accessory != null) {
                        //call method to set up accessory communication
                    }
                } else {
                    Log.d(TAG, "permission denied for accessory " + accessory);
                }
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

IntentFilter filter=new IntentFilter (ACTION_USB_PERMISSION);
registerReceiver (mUsbReceiver, filter);
```

显示向用户要求访问附件权限的对话框，需要使用 requestPermission() 方法，代码如下：

UsbAccessory accessory;
...
mUsbManager.requestPermission(accessory, mPermissionIntent);

**4）与 USB 附件进行通信。**

可以通过 UsbManager 示例获取一个文件描述符（FileDescriptor），并通过该文件描述符建立输入输出流，进而达到与 USB 附件通信的目的。

与 USB 附件通信的过程应该写在一个单独的线程里，避免阻塞 UI 线程。下面的代码演示了打开 USB 附件并进行通信的过程：

```

UsbAccessory mAccessory;
ParcelFileDescriptor mFileDescriptor;
FilelnputStream mlnputStream;
FileOutputStream mOutputStream;

...

private void openAccessory(){
    Log.d (TAG, "openAccessory:"+accessory) ; mFileDescriptor=mUsbManager.openAccessory (mAccessory);
    if (mFileDescriptor !=null) {
        FileDescriptor fd=mFileDescriptor.getFileDescriptor();
        mInputStream=new FilelnputStream (fd);
        mOutputStream=new FileOutputStream (fd) ;
        Thread thread=new Thread (null, this, "AccessoryThread");
        thread.start();
    }
}
```

在线程的 run() 方法中，可以通过 FileInputStream 和 FileOutputStream 进行读写附件的操作。

当从附件中使用 FileInputStream 读取信息时，应该保证用于保存读取数据的缓冲区的大小足够容纳 USB 数据包中的数据。

Android Accessory Protocol 中支持 USB 数据包缓冲区最大到 16384 字节。为了保证信息传输的安全，建议声明 16384 字节长度的缓冲区。

**5）结束与 USB 附件的通信。**

当与 USB 附件的通信结束或者附件被从系统中断开的时候，应该使用 close() 方法关闭文件描述符。

下面的代码定义了一个监听附件被移除的事件的 BroadcastReceiver：

```

BroadcastReceiver mUsbReceiver=new BroadcastReceiver(){
    public void onReceive (Context context, Intent intent) {
        String action=intent.getAction();
        if (UsbManager.ACTION_USB_ACCESSORY__DETACHED.equals (action)) {

            UsbAccessory accessory= (UsbAccessory) intent.getParcelableExtra (UsbManager.EXTRA_ACCESSORY) ;
            if (accessory !=null) {
                // call your method that cleans up and closes communication with the accessory
            }

        }
    }
};
```

在应用程序中创建该 BroadcastReceiver，而不是在应用程序的 Manifest 配置文件中，这样可以保证只有 Activity 运行时才对 USB 附件断开事件进行处理。而附件断开事件只会被发送给当前运行的 Activity，而不是被广播给所有的应用程序。