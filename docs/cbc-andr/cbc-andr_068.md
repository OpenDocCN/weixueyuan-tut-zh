# Android BackupAgent 实现数据备份与恢复

> 原文：[`c.biancheng.net/view/3142.html`](http://c.biancheng.net/view/3142.html)

大多数应用程序不需要直接继承 BackupAgent 类，而是继承 BackupAgentHelper 类，并利用 BackupAgentHelper 内建的 helper 类自动备份和恢复文件。

不过，以下三种情况需要直接继承 BackupAgent 类来实现备份代理：

*   将数据格式版本化。例如，需要在恢复数据时修正格式，可以建立一个备份代理，在数据恢复过程中，如果发现当前版本和备份时的版本不一致，可以执行必要的兼容性修正工作。
*   不是备份整个文件，而是指定备份部分数据以及恢复部分数据到设备。
*   备份数据库中的数据。若应用程序使用了 SQLite 数据库并且希望当用户重装应用程序时能够恢复数据库中的数据，则需要建立一个自定义的 BackupAgent。它在备份操作时从数据库中读取合适的数据，在恢复数据操作时建立数据表并插入数据。

通过继承 BackupAgent 类创建备份代理时，必须实现以下两个方法：

*   onBackup()：备份管理器在程序请求进行备份操作后将调用该方法。在该方法中实现从设备读取应用程序数据，并把需备份的数据传递给备份管理器的操作。
*   onRestore()：备份管理器在恢复数据时调用该方法。备份管理器调用该方法时将传入备份的数据，并通过该方法将数据恢复到设备上。

#### 1）备份数据

应用程序发出数据备份请求时，备份管理器将调用 onBackup() 方法。在此方法内必须把要备份的数据提供给备份管理器，然后将数据保存到云存储中。

只有备份管理器能够调用备份代理中的 onBackup() 方法。当数据发生改变并需要执行备份时，需要调用 dataChanged() 方法发起备份请求。

备份请求并不会立即导致 onBackup() 方法的调用，备份服务器会等待合适的时机，为上次备份操作后又发出备份请求的所有应用程序执行备份操作。

onBackup() 方法需要传入三个参数，所代表的意义分别如下。

| 名称 | 作用 |
| --- | --- |
| oldState | 表示已打开的、只读的文件描述符 ParcelFileDescriptor，指向应用程序提供的上次备份数据状态的文件。该文件不是来自云存储的备份数据，而是记录上次调用 onBackup() 备份数据相关状态信息的本地文件。 onBackup() 不能读取保存于云存储的数据，可以根据此信息来判断数据自上次备份以来是否变动过。 |
| Data | BackupDataOutput 对象，用于将要备份的数据传给备份管理器。 |
| newState | 表示已打开的、可读写的文件描述符 ParcelFileDescriptor，指向一个用于将提交给 data 参数的数据相关状态信息写入的文件，状态信息可以简单到只是文件的最后修改时间。 备份管理器下次调用 onBackup() 时，该对象作为 oldState 传入。若没有向 newState 写入信息，则备份管理器下次调用 onBackup() 时 oldState 将指向一个空文件。 |

利用以上参数可以实现 onBackup()，方法如下：

**1）**通过比较 oldState，检查自上次备份以来数据是否发生过改变。从 oldState 读取信息的方式取决于当时写入的方式（见步骤 3）。

最简单的记录文件状态的方式是写入文件的最后修改时间戳。以下是从 oldState 读取并比较时间戳的代码：

```

// Get the oldState input stream
FileInputStream instream=new FileInputStream (oldState.getFileDescriptor());
DataInputStream in = new DataInputStream (instream) ;

try {
    // Get the last modified timestamp from the state file and data file
    long stateModified = in.readLong();
    long fileModified = mDataFile.lastModified();

    if (stateModified != fileModified) {
        // The file has been modified, so do a backup
        // Or the time on the device changed, so be safe and do a backup
    } else {
        // Don't back up because the file hasn't changed return;
    }
} catch (IOException e) {
    // Unable to read state file... be safe and do a backup
}
```

如果数据没有发生变化，就不需要进行备份，请跳转到步骤 3。

2）在和 oldState 比较后，如果数据发生了变化，就把当前数据写入 data 以便将其返回并上传到云存储中。
必须以 BackupDataOutput 中的“entity“方式写入每一块数据。

一个 entity 是一个二进制数据记录，使用一个唯一的字符串键值进行标识。因此，所备份的数据集实际上是一组键值对。要在备份数据集中增加一个 entity，必须：

*   调用 writeEntityHeader() 方法，传入代表要写入数据的唯一字符串键值和数据大小。
*   调用 writeEntityData() 方法，传入存放着数据的字节缓冲区，以及需从缓冲区写入的字节数，该字节数应该与传给 writeEntityHeader() 的数据大小一致。

下面的示例代码演示了把一些数据拼接为字节流并写入一个 entity 的过程：

```

// Create buffer stream and data output stream for our data
ByteArrayOutputStream bufStream=new ByteArrayOutputStream();
DataOutputStream outWriter=new DataOutputStream(bufStream);
// Write structured data
outWriter.writeUTF(mPlayerName);
outWriter.writeInt(mPlayerScore);
// Send the data to the Backup Manager via the BackupDataOutput
byte[] buffer=bufStream.toByteArray();
int len=buffer.length;
data.writeEntityHeader(TOPSCORE_BACKUP_KEY, len);
data.writeEntityData(buffer, len);
```

需要备份的每一块数据都要执行一次该操作。如何将数据切分为 entity 由开发者决定。

**3）**无论是否执行了数据备份（步骤 2），都要把当前数据的状态信息写入 newState ParcelFileDescriptor 指向的文件内。备份管理器会在本地保持此对象，以代表当前备份数据。

下次调用 onBackup() 时，此对象作为 oldState 返回给应用程序，由此可以决定是否需要再做一次备份（如步骤 1 所述）。如果不把当前数据的状态写入此文件，下次调用时 oldState 将返回空值。

以下示例代码把文件的最后修改时间戳作为当前数据的状态存入 newState：

```

FileOutputStream outstream=new FileOutputStream(newState.getFileDescriptor());
DataOutputStream out=new DataOutputStream(outstream);
long modified=mDataFile.lastModified();
out.writeLong(modified);

```

需要注意的是，如果应用程序数据存放于文件中，需要使用同步语句（synchronized）来访问文件。这样在应用程序的 Activity 进行写文件操作时，备份代理就不会去读文件了。

#### 执行数据恢复操作

恢复程序数据时，备份管理器将调用备份代理的 onRestore() 方法。调用此方法时，备份管理器会把在云存储备份的数据传入，以供恢复到设备中去。

只有备份服务器能够调用 onRestore() 方法，在系统安装应用程序并且发现有备份数据存在时，数据恢复操作会自动发生。此外，应用程序也可以通过调用 requestRestore() 方法来发起恢复数据的请求。

当备份管理器调用 onRestore() 方法时，传入以下三个参数。

*   Data：BackupDataInput 对象，用以读取备份数据。
*   appVersionCode：整型数据，表示备份数据时，应用程序的 manifest 的 android:versionCode 属性。可以用于核对当前应用程序版本并确定数据格式的兼容性。
*   newState：已打开的、可读写的文件描述符 ParcelFileDescriptor，指向一个文件，用于写入最后一次提交 data 数据的备份状态。本对象在下次调用 onBackup() 方法时作为 oldState 返回。

在实现 onRestore() 时，应该对 data 调用 readNextHeader()，以遍历数据集里所有的 entity。对其中每个 entity 需进行以下操作：

1）用 getKey() 方法获取 entity 的键值。

2）将此 entity 键值和已知键值清单进行比较，这个清单应该已经在 BackupAgent 继承类中作为字符串常量定义。一旦键值匹配其中一个键，就执行读取 entity 数据并保存到设备的操作：

*   用 getDataSize() 读取 entity 数据大小并据其创建字节数组。
*   调用 readEntityData()，传入字节数组作为获取数据的缓冲区，并指定起始位置和读取字节数。
*   字节数组将被填入数据，按需读取数据并写入设备即可。

3）把数据读出并写回设备以后，把数据的状态写入 newState 参数。

下面的实例代码将前面的例子中所备份的数据进行了恢复：

```

@Override
public void onRestore (BackupDatalnput data, int appVersionCode,ParcelFileDescriptor newState) throws IOException {
    // There should be only one entity, but the safest // way to consume it is using a while loop
    while (data.readNextHeader ()) {
        String key=data.getKey();
        int dataSize=data.getDataSize ();

        // If the key is ours (for saving top score) . Note this key was used when
        // we wrote the backup entity header
        if (TOPSCORE_BACKUP_KEY.equals (key)) {
            // Create an input stream for the BackupDatalnput
            byte[] dataBuf=new byte[dataSize];
            data.readEntityData (dataBuf, 0, dataSize) ;
            ByteArraylnputStream baStream=new ByteArraylnputStream (dataBuf) ; DatalnputStream in=new DatalnputStream (baStream) ;

            // Read the player name and score from the backup data
            mPlayerName=in.readUTF(); mPlayerScore=in.readlnt();

            // Record the score on the device (to a file or something)
            recordScore (mPlayerName, mPlayerScore) ;
        } else {
            // We don't know this entity key. Skip it. (Shouldn't happen.)
            data.skipEntityData();
        }
    }

    // Finally, write to the state blob (newState) that describes the restored data
    FileOutputStream outstream=new FileOutputStream (newState.getFileDescriptor());
    DataOutputStream out=new DataOutputStream (outstream);
    out.writeUTF (mPlayerName);
    out.writelnt (mPlayerScore);
}
```

在以上代码中，传给 onRestore() 的 appVersionCode 参数没有被用到。如果用户程序的版本降低，比如从 1.5 降到 1.0，可能就会用此参数来选择备份数据。