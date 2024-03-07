# Android APP 数据备份与恢复

> 原文：[`c.biancheng.net/view/3135.html`](http://c.biancheng.net/view/3135.html)

Android 的备份服务允许用户将应用程序的持久化数据复制到远端云存储，以便为应用程序的数据和设置信息创建一个还原点。

如果用户为设备恢复了出厂设置或者更换了新的 Android 设备，当安装应用程序时，系统会自动恢复用户备份的数据到应用程序。这样用户就不需要人为复制之前的数据和配置信息。该过程对用户完全透明，不会影响应用程序的功能和用户体验。

当应用程序发起备份请求时，Android 的备份管理器 BackupManager 会查询需要备份的应用程序数据，并将其交给备份传输器，再由备份传输器将数据传输到云存储保存起来。

当执行恢复操作时，备份管理器从备份传输器获取备份数据并交还给应用程序，由应用程序将备份数据恢复到设备。

恢复操作请求可以由应用程序发起，但并不是必需的。如果应用程序被安装并且存在与用户关联的备份数据，当用户重置手机的所有配置或者升级到新的设备时，Android 系统都会自动执行数据恢复操作。

需要注意的是，备份服务并不是为了与其他设备同步数据或者在应用程序的正常生命周期中保存数据而设计的。

备份的数据不能被随意访问和改写，必须要使用备份管理器提供的 API 进行访问。

备份传输器是 Android 数据备份框架的客户端组件，由设备制造商和服务提供商共同定制。

备份传输器可能因设备的不同而不同，并且对设备而言是透明的。

备份管理器将应用程序和备份传输器分离开来，应用程序通过固定的 API 与备份管理器进行通信， 而不考虑底层传输的具体实现过程。

数据备份功能并不保证在所有的 Android 设备上都支持。但是，即使设备不支持数据备份，应用程序也会正常运行，只是不能接收备份管理器的备份请求对数据进行备份而已。

备份的数据不会被设备上的其他应用程序访问，只有备份管理器和备份传输器有权限访问备份的数据。

另外，由于云存储数据传输服务因设备的不同而不同，因此 Android 系统不能保证备份数据的安全性。所以当用户使用云存储备份敏感数据时（例如用户名和密码），应三思而行。

## 实现备份代理的步骤

为了备份应用程序数据，需要使用备份代理。

备份代理将被备份管理器调用，用于提供所需备份的数据。当应用程序被重新安装时，备份管理器还要调用此备份代理来恢复应用程序的数据。

备份管理器通过备份传输器处理所有 Android 设备与云存储之间的数据传输工作，而备份代理则负责所有对 设备上数据的处理。

实现备份代理需要经过以下步骤：

#### **1. **在 manifest 文件中用 android:backupAgent 属性声明备份代理

相关代码如下：

```

<manifest...>
    ...
    <application android:label="MyApplication"
                 android:backupAgent="MyBackupAgent">
        <activity ...>
            ...
        </activity>
    </application>
</manifest>
```

以上代码为应用程序声明了一个名为 MyBackupAgent 的备份代理。

#### **2. **为 Android 备份服务进行注册

Google 为 Android 2.2 以上版本的设备提供了利用 Android 备份服务进行备份传输的服务。

应用程序要利用 Android 备份服务执行备份操作，必须对应用程序进行注册以获得一个备份服务的 Backup Service Key，然后在 Android manifest 文件中声明这个 Key。

要获取 Backup Service Key，需要到 https://developers.google.com/android/backup/signup? hl=zh-CN 为 Android 服务进行注册。

注册时会得到一个 Backup Service Key 和 Android manifest 文件内相应的 <meta-data> XML 代码，这段代码必须包含在 <application> 元素下。相关示例代码如下：

```

<application android:label="MyApplication"
    android:backupAgent="MyBackupAgent">
    ...
    <meta-data  android:name="com.google.android.backup.api_key"
        android:value="AEdPqrEAAAAIDaYEVgU6DJnyJdBmU7KLH3kszDXLv_4DIsEIyQ"/>
</application>
```

android:name 必须是 com.google.android.backup.api_key，android:value 也必须是注册 Android 备份服务时获得的 Backup Service Key。

如果存在多个应用程序，就需要根据每个程序的 package name 分别为每一个应用程序进行注册。

#### **3. **实现备份代理

实现备份代理有两种方式，分别为：

**1）继承 BackupAgent**

BackupAgent 类提供了核心接口，应用程序通过这些接口与备份管理器进行通信。

若直接继承此类，则必须覆盖 onBackup() 和 onRestore() 方法来处理数据的备份和恢复操作。

**2）继承 BackupAgentHelper**

BackupAgentHelper 类提供了 BackupAgent 类的易用性封装，大幅度减少了需编写的代码数量。

在 BackupAgentHelper 内，必须用一个或多个 helper 对象来自动备份和恢复特定类型的数据，因此不再需要实现 onBackup() 和 onRestore() 方法了。

Android 目前提供两种 backup helper，分别用于 SharedPreferences 和内部存储的备份和恢复操作。