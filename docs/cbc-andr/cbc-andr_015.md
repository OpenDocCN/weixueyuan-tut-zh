# AndroidManifest.xml 配置文件详解

> 原文：[`c.biancheng.net/view/2954.html`](http://c.biancheng.net/view/2954.html)

每一个 Android 项目都包含一个清单（Manifest）文件 AndroidManifest.xml，它是 XML 格式的 Android 程序声明文件，每个 Android 程序必须在根目录下包含一个 AndroidManifest.xml。

AndroidManifest.xml 包含 Android 系统运行程序前所必须掌握的重要信息，这些信息包含应用程序名称、图标、包名称、模块组成、授权和 SDK 最低版本等。

例如，Manifest 文件可以使用如下代码声明一个 Activity：

```

<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android = "http://schemas.android.com/apk/res/android"
    package = "demo.hbyz.myapplication3">

    <application
        android:allowBackup = "true"
        android:icon = "@mipmap/ic_launcher"
        android:label = "@string/app_name"
        android:roundIcon = "@mipmap/ic_launcher_round"
        android:supportsRtl = "true"
        android:theme = "@style/AppTheme">

        <activity android:name = ".MainActivity">
            <intent-filter>
                <action android:name = "android.intent.action.MAIN" />
                <category android:name = "android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

    </application>

</manifest>
```

AndroidManifest.xml 中可包含的所有标签元素如以下代码所示，其中除了 <manifest> 和 <application> 标签是必需的，其他所有标签都可按情况添加。

```

<?xml version="1.0" encoding="utf-8"?>

<manifest>

    <uses-permission />
    <permission />
    <permission-tree />
    <permission-group />
    <instrumentation />
    <uses-sdk />
    <uses-configuration /> 
    <uses-feature /> 
    <supports-screens /> 
    <compatible-screens /> 
    <supports-gl-texture /> 

    <application>

        <activity>
            <intent-filter>
                <action />
                <category />
                <data />
            </intent-filter>
            <meta-data />
        </activity>

        <activity-alias>
            <intent-filter>. . .</intent-filter>
            <meta-data />
        </activity-alias>

        <service>
            <intent-filter>. . .</intent-filter>
            <meta-data/>
        </service>

        <receiver>
            <intent-filter>. . .</intent-filter>
            <meta-data />
        </receiver>

        <provider>
            <grant-uri-permission />
            <meta-data />
            <path-permission />
        </provider>

        <uses-library />

    </application>

</manifest>
```

在此，仅对几种常见的标签进行简单介绍。

#### 1) manifest 标签

manifest 标签是 AndroidManifest.xml 文件的根标签。

manifest 标签用于设置与项目相关的一些属性，比如用于唯一标识应用程序的 package 属性，用于记录应用程序版本的 Android:versionName 属性等等。其中的 xmlns:Android 属性必须被定义为“http://schemas.Android.com/apk/res/Android”。

#### 2) application 标签

manifest 标签仅能包含一个 application 标签。

manifest 标签使用各种属性来指定应用程序的各种元数据（包括标题、图标和主题）。它还可以作为一个包含活动（Activity）、服务（Service）、内容提供器（Provider）和广播接收器（Broadcast Receiver）标签的容器，用来指定应用程序组件。

application 标签还可以包含很多子标签，下面我们来介绍一下它的子标签。它的子标签如下：

| 名称 | 作用 |
| activity 标签 | 应用程序显示的每一个 Activity 都要求有一个 activity 标签，并使用 Android:name 属性来指定类的名称。这必须包含核心的启动 Activity 和其他所有可以显示的屏幕或者对话框。启动任何一个没有在清单中定义的 Activity 时都会抛出一个运行时异常。每一个 Activity 节点都允许使用 intent-filter 子标签来指定哪个 Intent 启动该活动。 |
| service 标签 | 和 activity 标签一样，应用程序中使用的每一个 Service 类都要创建一个新的 service 标签。（Service 标签也支持使用 intent-filter 子标签来允许后面的运行时绑定。） |
| provider 标签 | provider 标签用来说明应用程序中的每一个内容提供器。内容提供器是用来管理数据库访问以及程序内和程序间共享的。 |
| receiver 标签 | 通过添加 receiver 标签，可以注册一个广播接收器，而不用事先启动应用程序。广播接收器就像全局事件监听器一样，一旦注册了之后，无论何时，只要与它相匹配的 Intent 被应用程序广播出来，它就会立即执行。通过在声明中注册一个广播接收器，可以使这个进程实现完全自动化。如果一个匹配的 Intent 被广播了，应用程序就会自动启动，并且你注册的广播接收器也会开始运行。 |

#### 3) uses-permission 标签

作为安全模型的一部分，uses-permission 标签声明了那些自己定义的权限，而这些权限是应用程序正常执行所必需的。在安装程序时，设定的所有权限将会告诉用户，由他们来决定同意与否。

对很多本地 Android 服务来说，权限都是必需的，特别是那些需要付费或者有安全问题的服务（例如拨号、接收 SMS 或者使用基于位置的服务）。第三方应用程序，包括你自己的应用程序，也可以在提供对共享的程序组件进行访问之前指定权限。

#### 4) permission 标签

在可以限制访问某个应用程序组件之前，需要在清单中定义一个 permission。

可以使用 permission 标签来创建这些权限定义。然后，应用程序组件就可以通过添加 Android: permission 属性来要求这些权限。其他的应用程序需要在它们的清单中包含 uses-permission 标签（并且通过授权），之后才能使用这些受保护的组件。

在 permission 标签内，可以详细指定允许的访问权限的级别（normal、dangerous、signature 和 signatureOrSystem）、一个 label 属性和一个外部资源，这个外部资源应该包含对授予这种权限的风险的描述。

#### 5) instrumentation 标签

instrumentation 类提供一个框架，用来在应用程序运行时在活动或者服务上运行测试。

instrumentation 类提供了一些方法来监控应用程序及其与系统资源的交互。对于为自己的应用程序所创建的每一个测试类，都需要创建一个新的节点。