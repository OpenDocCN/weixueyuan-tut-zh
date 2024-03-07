# Android BackupAgentHelper 实现数据备份与恢复

> 原文：[`c.biancheng.net/view/3139.html`](http://c.biancheng.net/view/3139.html)

上一节教程我们学习了怎么使用 BackupAgent 实现 Android 数据备份与恢复，这一节我们主要介绍如何使用 BackupAgentHelper 实现 Android 数据备份与恢复。

#### 1) 实现 BackupAgentHelper

如果要备份整个 SharedPreferences 和内部存储文件，就应该使用 BackupAgentHelper 的子类创建备份代理，这种方式不需要实现 onBackup() 和 onRestore() 方法，因此可以比使用 BackupAgent 实现的方式大幅度地降低代码量。

BackupAgentHelper 的实现必须要使用一个或多个 backup helper。backup helper 是一种专用组件，BackupAgentHelper 用它来对特定类型的数据执行备份和恢复操作。

Android 框架目前提供两种 helper，分别为：

*   SharedPreferencesBackupHelper 用于备份 SharedPreferences 文件。
*   FileBackupHelper 用于备份内部存储文件。

BackupAgentHelper 对象中可包含多个 helper，但对于每种数据类型只需用到一个 helper。也就是说，即使存在多个 SharedPreferences 文件，也只需要一个 SharedPreferences-BackupHelper 即可完成对文件的备份与恢复。

要在 BackupAgentHelper 中加入 helper，需要在 onCreate() 方法中执行以下步骤：
**1)** 实例化所需的 helper，并在其构造方法中指定要备份的文件。
**2)** 调用 addHelper() 方法，把 helper 加入 BackupAgentHelper。

使用 SharedPreferencesBackupHelper 备份 SharedPreferences 的相关代码如下：

```

public class MyPrefsBackupAgent extends BackupAgentHelper {
    // The name of the SharedPreferences file
    static final String PREFS="user_preferences";

    // A key to uniquely identify the set of backup data static final String PREFS_BACKUP_KEY="prefs";

    // Allocate a helper and add it to the backup agent @Override
    public void onCreate(){
        SharedPreferencesBackupHelper helper=new SharedPreferencesBackupHelper (this, PREFS); addHelper (PREFS_BACKUP_KEY, helper);
    }
}
```

上面的这几行代码实现了一个完整的备份代理 MyPrefsBackupAgent。

*   user_preferences 是要备份的文件名称。
*   SharedPreferencesBackupHelper 对象包含备份和恢复 SharedPreferences 文件的所有代码，不再需要开发者人为书写。
*   当备份管理器调用 onBackup() 方法和 onRestore() 方法时，MyPrefsBackupAgent 会调用 helper 来完成对指定文件的备份和恢复操作。

由于 SharedPreferences 是线程安全的，因此可以从备份代理和其他 Activity 中安全地读写 shared preferences 文件。

使用 FileBackupHelper 备份内部存储文件的相关代码如下：

```

public class MyFileBackupAgent extends BackupAgentHelper {
    // The name of the files
    static final String TOP_SCORES="scores";
    static final String PLAYER_STATS="stats";

    // A key to uniquely identify the set of backup data static final String FILES_BACKUP_KEY="myfiles";

    // Allocate a helper and add it to the backup agent void onCreate(){
        FileBackupHelper helper=new FileBackupHelper (this, TOP_SCORES, PLAYER_STATS);
        addHelper (FILES_BACKUP_KEY, helper) ;
    }
}
```

其中，scores 和 stats 是要保存的文件名字，上面的代码定义的 helper 同时对两个文件进行备份和恢复操作。

需要注意的是，由于读写内部存储文件不是线程安全的，因此要确保当 Activity 在进行文件操作时，备份代理不会去读写该文件，每次读写文件时应该使用同步语句。

比如在 Activity 读写文件时，需要用一个对象作为同步语句的内部锁。事实证明，长度为零的数组要比普通对象更轻量化，更适合作为对象锁使用。

创建对象锁的代码如下：

// 内部锁对象
static final Object[] sDataLock=new Object[0];

然后，每次读写文件时就用这个锁对象创建同步语句。把游戏分数写入文件的同步代码如下：

```

try {
    synchronized (MyActivity.sDataLock) {
        File dataFile=new File (getFilesDir(), TOP_SCORES);
        RandomAccessFile raFile=new RandomAccessFile (dataFile,"rw");
        raFile.writeInt (score);
    }
} catch (IOException e) {
    Log.e (TAG,"Unable to write to file") ;
}
```

当读文件时，也应该使用同一个锁对象创建同步语句。

然后，需要覆盖 BackupAgentHelper 的 onBackup() 和 onRestore() 方法，用同一个内部锁同步备份和恢复操作。

上例中定义的 MyFileBackupAgent 需要添加以下方法：

```

@Override
public void onBackup (ParcelFileDescriptor oldState, BackupDataOutput data, ParcelFileDescriptor newState) throws IOException {
    //Hold the lock while the FileBackupHelper performs backup
    synchronized (MyActivity.sDataLock) {
        super.onBackup (oldState, data, newState);
    }
}

@Override
public void onRestore (BackupDatalnput data, int appVersionCode, ParcelFileDescriptor newState) throws IOException {
    //Hold the lock while the FileBackupHelper restores the file
    synchronized (MyActivity.sDataLock) {
        super.onRestore (data, appVersionCode, newState);
    }
}
```

至此，完成了使用 FileBackupHelper 进行文件备份和恢复操作的全部工作。

#### 2) 核实恢复数据的版本

在把数据备份到云存储的过程中，备份管理器会自动包含应用程序的版本号。

版本号是由 manifest 文件中的 android:versionCode 属性定义的。在调用备份代理恢复数据之前，备份管理器会查询已安装程序的 android:versionCode 属性，并与记录在备份数据中的版本号相比较。

如果备份数据的版本比设备上高，就意味着用户安装了旧版本的应用程序。这时备份管理器将停止数据恢复操作，onRestore() 方法也不会被调用，因为把数据恢复给旧版本的应用程序是没有意义的。

通过 android:restoreAnyVersion 属性可以替换以上规则。此属性用 true 或 false 标明是否在进行数据恢复时核实数据集的版本。

*   默认值是 false。
*   值为 true 时，备份管理器将忽略 android:versionCode 属性并且调用 onRestore() 方法。这时候应该在 onRestore() 方法中人工核实版本信息，并在版本冲突时采取必要的措施保证数据的兼容性。

为了便于在恢复数据时对版本号进行判断，onRestore() 方法把备份数据的版本号作为 appVersionCode 参数和数据一起传入方法中。

通过 PackageInfo.versionCode 可以查询到当前应用程序的版本号，相关代码如下：

```

PackageInfo info;
try {
    String name=getPackageName();
    info=getPackageManager().getPackageInfo(name,0);
}catch(NameNotFoundException nnfe){
    info=null;
}

int version;
if(info !=null){
    version=info.versionCode;
}
```

然后比较一下传入 onRestore() 方法的 appVersionCode 和 PackageInfo 的 version 值即可。

#### 3) 请求数据备份

应用程序在任何时候都可以通过调用 dataChanged() 方法来发起备份请求。

dataChanged() 方法将通知备份管理器用备份代理来备份数据。

备份管理器将会在合适的时候调用备份代理的 onBackup() 方法。

通常每次数据发生变化时都应该请求备份数据，但是实际的备份次数比请求次数少得多。

如果在备份管理器执行备份操作前连续请求了很多次，备份代理仅会执行一次数据备份操作。

#### 4) 请求数据恢复

在应用程序正常的生命周期内，应该不需要发起恢复数据的请求。在程序安装完成时，系统会自动检查备份数据并执行数据恢复操作。

在必要时，可以通过调用 requestRestore() 方法人工发起恢复数据的请求。这时，备份管理器会调用 onRestore() 方法进行数据恢复。