# Android 数据存储之 SharedPreferences

> 原文：[`c.biancheng.net/view/3099.html`](http://c.biancheng.net/view/3099.html)

无论是桌面应用程序还是 Android 手机应用程序，都会涉及数据的存储。下面我们将会详细介绍在 Android 中存储数据的相关知识。

在 Android 中应用程序存储的数据（包括文件）都属于应用程序私有，但同时也提供了 ContentProviders（数据共享），方便应用程序将私有的数据分享给其他程序使用。

数据存储方式共分为 5 种，分别为：

*   SharedPreferences。
*   内部存储（Internal Storage）。
*   外部存储（External Storage）。
*   SQLite 数据库存储。
*   网络存储。

其中，网络存储在本质上是对网络资源的获取和访问，其相关内容会在网络编程教程中进行介绍。

此外，Android 系统框架提供了 ContentProvider 来实现各种应用程序间持久化数据的共享。

下面我们先来介绍一下第一种 SharedPreferences。

## SharedPreferences

SharedPreferences 是 Android 系统提供的一个通用的数据持久化框架，用于存储和读取 key-value 类型的原始基本数据对。

目前仅支持 boolean、float、int、long 和 string 等基本类型的存储，对于自定义的复合数据类型，是无法使用 SharedPreferences 进行存储的。

SharedPreferences 主要用于存储系统的配置信息，类似于 Windows 下常用的 .ini 文件。

例如上次登录的用户名、上次最后设置的信息等，通过保存上一次用户所做的修改或者自定义参数设定，当再次启动程序后依然保持原有设置。它是用键值对的方式存储的，方便管理写入和读取。

使用 SharedPreferences 的步骤如下：

#### 1) 获取 Preferences

每个 Activity 默认都有一个 SharedPreferences 对象，获取 SharedPreferences 对象的方法有两种：

1）SharedPreferences getSharedPreferences(String name, int mode)。

使用该方法获取 name 指定的 SharedPreferences 对象，并获取对该 SharedPreferences 对象的读写控制权。

当应用程序中可能使用到多个 SharedPreferences 时使用该方法。

2）SharedPreferences getPreferences(int mode)。

当应用程序中仅需要一个 SharedPreferences 对象时，使用该方法获取当前 Activity 对应的 SharedPreferences，而不需要指定 SharedPreferences 的名字。

其中，参数 mode 有 4 种取值，分别是：

*   MODE_PRIVATE：默认方式，只能被创建的应用程序或者与创建的应用程序具有相同用户 ID 的应用程序访问。
*   MODE_WORLD_READABLE：允许其他应用程序对该 SharedPreferences 文件进行读操作。
*   MODE_WORLD_WRITEABLE：允许其他应用程序对该 SharedPreferences 文件进行写操作。
*   MODE_MULTI_PROCESS：在多进程应用程序中，当多个进程都对同一个 SharedPreferences 进行访问时，该文件的每次修改都会被重新核对。

#### 2) 获取 SharedPreferences.Editor

调用 edit() 方法获取 SharedPreferences.Editor，SharedPreferences 通过该接口对其内容进行更新。

#### 3) 更新 SharedPreferences

通过 SharedPreferences.Editor 接口提供的 put 方法对 SharedPreferences 进行更新。

例如使用 putBoolean(String key, boolean value)、putFloat(String key, float value) 等方法将相应数据类型的数据与其 key 对应起来。

#### 4) 提交

调用 SharedPreferences.Editor 的 commit() 方法将更新提交到 SharedPreferences 中。

## 使用 SharedPreferences

实例 SharedPreferencesDemo 演示了 SharedPreferences 对象的使用方法。

该实例的运行效果如图 1 所示。
![SharedPreferencesDemo](img/0e0e89b179b1d0a2fa9b93580ef938cf.png)
图 1  SharedPreferencesDemo
当用户在该实例运行时，在文本框中输入电话号码和所在城市，例如 13800000000 和 beijing，单击回退按钮退出应用程序时，该应用程序将相关信息写入其对应的 SharedPreferences 中。

当用户再次启动该应用程序时，之前填写到文本框内的信息会被从 SharedPreferences 中读取并显示出来，以方便用户修改。

实例 SharedPreferencesDemo 中的布局文件 main.xml 中放置了三个 TextView 和两个 EditText，其中两个 EditText 按照 TextView 的要求输入电话号码和城市。其代码如下：

```

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical">

    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="使用 Shared Preferences 存储程序信息" />

    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="您的电话号码" />

    <EditText
        android:id="@+id/phone_text"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:hint="输入电话号码" />

    <EditText
        android:id="@+id/city_text"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:hint="输入城市名称" />

</LinearLayout>
```

实例 SharedPreferencesDemo 中 AndroidManifest.xml 的代码如下：

```

<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="introduction.android.SharedPreferencesDemo"
    android:versionCode="1"
    android:versionName="1.0">

    <uses-sdk android:minSdkVersion="14" />
    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```

实例 SharedPreferencesDemo 中 MainActivity.java 的代码如下：

```

import javax.security.auth.PrivateCredentialPermission;

import android.app.Activity;
import android.content.SharedPreferences;
import android.os.Bundle;
import android.widget.EditText;

public class MainActivity extends Activity {
    private EditText phoneText, cityText;
    private String phone, city;
    public static final String SET_INFO = "SET_Info";
    public static final String PHONE = "PHONE";
    public static final String CITY = "CITY";

    @Override

    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        phoneText = (EditText) findViewById(R.id.phone_text);
        cityText = (EditText) findViewById(R.id.city_text);
        /*获取 Shared Preferences 对象*/
        SharedPreferences setinfo = getPreferences(Activity.MODE_PRIVATE);
        /*取出保存的电话号码和地址信息*/
        phone = setinfo.getString(PHONE, "");
        city = setinfo.getString(CITY, "");
        /*将取出的信息分别放在对应的 EditText 中*/
        phoneText.setText(phone);
        cityText.setText(city);
    }

    @Override
    protected void onStop() {
        SharedPreferences setinfo = getPreferences(Activity.MODE_PRIVATE);
        setinfo.edit()
                .putString(PHONE, phoneText.getText().toString())
                .putString(CITY, cityText.getText().toString())
                .commit();
        super.onStop();
    }
}
```

该 Activity 在启动时通过 onCreate() 方法从其 SharedPreferences 中获取相应数据，在 onStop() 方法中将相应数据写入 SharedPreferences 中，其中：

SharedPreferences setinfo=getPreferences(Activity.MODE_PRIVATE);

用于获取当前 Activity 默认的 SharedPreferences 对象，该对象没有名字。当然，也可以通过 getSharedPreferences(String name, int mode) 方法来创建并获取一个带有名字的 SharedPreferences。

该 SharedPreferences 被创建后，可以在应用程序的包路径下，即 data/data/<your package name>/shared_prefs 文件夹下找到该文件。