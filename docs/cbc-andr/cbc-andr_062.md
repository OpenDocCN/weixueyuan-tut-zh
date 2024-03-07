# Android ContentProvider（内容提供者）

> 原文：[`c.biancheng.net/view/3113.html`](http://c.biancheng.net/view/3113.html)

ContentProvider 是 Android 的四大组件之一，用于保存和检索数据，是 Android 系统中不同应用程序之间共享数据的接口。

在 Android 系统中，应用程序之间是相互独立的，分别运行在自己的进程中，相互之间没有数据交换。若应用程序之间需要共享数据，就要用到 ContentProvider。

在 Android 系统的手机中，ContentProvider 最典型的应用是，当发送一条短信时，需要用到联系人的相关信息，此时就是通过 ContentProvider 提供的接口访问 Android 系统中的电话簿，并从中选择联系人。

ContentProvider 提供了一组应用程序之间能相互访问的接口。应用程序通过 ContentProvider 把当前应用中的数据共享给其他应用程序访问，而其他应用程序通过 ContentProvider 对指定应用中的数据进行访问和操作。

Android 系统对一系列常见的公用数据类型提供了对应的 ContentProvider 接口，例如视频、音频、图像、个人通信信息等，都定义在 android.provider 包下。

若应用程序开发者想将自己的数据公开给其他应用程序使用，有两种方法：

*   定义自己的 ContentProvider 子类。
*   将当前应用程序的数据添加到已有的 ContentProvider 中。

ContentProvider 中的数据在形式上和关系数据库中的表格很相似。

以 Android 系统内建的用户常用词典所对应的 ContentProvider 为例，Android 系统为其定义的名字为 android.provider.UserDictionary，该用户词典中的 Word 表格记录了特定用户经常使用的不规则单次的相关信息。其数据格式如下表所示。

**ContentProvider 数据格式**

| word | appId | frequency | locale | _ID |
| --- | --- | --- | --- | --- |
| manpreduce | user1 | 100 | en_US | 1 |
| precompiler | user14 | 200 | fr_FR | 2 |
| applet | user2 | 225 | fr_CA | 3 |
| const | user1 | 255 | pt_BR | 4 |
| int | user5 | 100 | en_UK | 5 |

表头部分存储在 ContentProvider 中，表格的每一行是该词典数据的一个实例，也就是一个非标准的单词，每一列是和该单词相关的一些信息，例如该单词的拼写、使用者的 id、使用频率等，_ID 起到了主键的作用。

应用程序通过 ContentResolver 的对象访问 ContentProvider 中的数据，该对象提供了对持久层数据的 CRUD 方法。

每个 Activity 都有一个 ContentResolver 对象，要获取该对象，可以使用 Activity 提供的 getContentResolver() 方法。

当然，应用程序要使用其他应用程序提供的 ContentProvider，需要拥有进行操作的相应权限。所有用户词典数据的代码为：

mCursor=getContentResolver().query(UserDictionary.Words.CONTENT_URI,null,null,null,null)

其所对应的权限为：android.permission.READ_USER_DICTIONARY，因此必须在应用程序的 AndroidManifest.xml 文件中添加：

<uses-permission android:name="android.permission.READ_USER_DICTIONARY">

UserDictionary.Words.CONTENT_URI 指的是用户词典中 words 表的内容 URI。

ContentProvider 通过 URI 来共享数据。URI 是一个通用资源标志符，可将其分为 A、B、C、D 共 4 部分。

*   A：无法改变的标准前缀，包括“content://”“tel://”等。当前缀是“content://”时，说明在通过一个 ContentProvider 控制这些数据。
*   B：URI 的授权部分，一般为 ContentProvider 的全称，它通过 Android:authorities 属性声明，用于说明是哪个 ContentProvider 类提供这些数据，必须全部由小写字母组成，如 content://introduction.android.myprovider。
*   C：路径，可以理解为需要操作的数据库中表的名字，如“content:// introduction. android.myprovider /name”中的 name。
*   D：若 URI 中包含表示需要获取记录的 ID，则返回该 ID 对应的数据，若没有 ID，则表示返回全部数据，如 content://introduction.android.myprovider/name/01。

在本实例中，UserDictionary.Words.CONTENT_URI 包含所要访问 ContentProvider 的标识和具体信息表的路径。

其所代表的完整的字符串是“content://user_dictionary/words”，其中“content://”是前置格式字符串，即 A 部分；“user_dictionary”指定了提供数据的 ContentProvider，即 B 部分；“words”指定了要访问的数据表，即 C 部分。

此外，ContentProvider 允许通过在 URI 后面添加 ID 值的方式访问数据表中某一列数据，即添加 D 部分。例如，访问用户词典 words 表中 _ID=2 的数据的 URI 可以这样表示：

Uri singleUri=ContentUri.withAppendedId(UserDictionary.Words.CONTENT_URI,2);

其对应的完整 URI 为：“content://user_dictionary/words/2”。

ContentProvider 定义在 android.content 包下面，是一个抽象类。定义一个 Content Provider 必须实现下面几个抽象方法。

| 方法名称 | 作用 |
| --- | --- |
| onCreate() | 用于在启动时初始化 ContentProvider。由于该方法会在应用程序的主线程启动时被调用，因此不应该执行耗时的操作，以免延迟应用程序的启动时间。执行成功返回 true，失败返回 false。 |
| query(Uri,String[],String,String[],String) | 用于对 Uri 指定的 ContentProvider 进行查询，返回一个 Cursor 对象。 |
| insert(Uri, ContentValues) | 用于添加数据到 Uri 指定的 ContentProvider 中。 |
| update(Uri, ContentValues, String, String[]) | 用于更新 Uri 指定的 ContentProvider 中的数据。 |
| delete(Uri, String, String[]) | 用于从 Uri 指定的 ContentProvider 中删除数据。 |
| getType(Uri) | 用于返回 Uri 指定的 ContentProvider 中的数据的 MIME 类型。 |

ContentResolver 提供的方法和 ContentProvider 提供的方法相对应，主要有以下几个方法。

| 方法名称 | 作用 |
| --- | --- |
| query(Uri uri, String[] projection, String selection, String[] selectionArgs, String sortOrder) | 用于对 Uri 指定的 ContentProvider 进行查询。 |
| insert(Uri uri, ContentValues values) | 用于添加数据到 Uri 指定的 ContentProvider 中。 |
| delete(Uri uri, String selection, String[] selectionArgs) | 用于从 Uri 指定的 ContentProvider 中删除数据。 |
| update(Uri uri, ContentValues values, String selection, String[] selectionArgs) | 用于更新 Uri 指定的 ContentProvider 中的数据。 |

在对某特定 ContentProvider 的 CRUD 操作中，通过 ContentResolver 提供的 CRUD 方法将相关信息传递给 ContentProvider，所提供的 CRUD 方法进而对数据进行操作。

因此，在定义自己的 ContentProvider 时，应该定义好该 ContentProvider 对数据进行 CRUD 操作时所使用的方法。

## UriMatcher

Android 系统提供了 UriMatcher 类用于对 URI 的匹配。

使用步骤为：
1）创建 UriMatcher 类对象。
2）通过 UriMatcher.addURI(String,String,int) 方法对其增加需要匹配的 URI 路径，所对应的匹配码由第三个参数指定。
3）通过 UriMatcher.match(Uri) 方法进行匹配，并返回匹配码。

其代码如下：

```

 UriMatcher uriMatcher=new UriMatcher (UriMatcher.NO_MATCH) ;
//构建 UriMatcher 对象，常量 UriMatcher .NO_MATCH 表示不匹配任何路径，返回码-1
//添加需要匹配的 URI，并指定匹配时返回的匹配码
UriMatcher.addURI ("introdcuton.android.myprovider", "text", 1) ;
//荣国 match()方法匹配 content://introdcuton .android.myprovider/text/230
//路径，对应匹配码为 2
uriMatcher.addURI ("introdcuton.android.myprovider", "text/#", 2) ;
//#号为通配符，如果 match()方法匹配 content://introdcuton.android.myprovider/text/230
//路径，对应匹配码为 2
Uri uri=Uri.parse ("content://introdcuton.android.myprovider/text/10") ;
switch (uriMatcher.match (uri)) {
    case 1: //匹配操作码为 1
        //执行相应操作
        break;
    case 2: //匹配返回码为 2
        //执行相应的操作
        break;
    default: //不匹配
        //执行相应的操作
        break;
}
```

上述代码中，uriMatcher.addURI("introdcuton.android.myprovider","text/#",2) 中“#”为通配符，代表任意数字，还可以使用通配符“*”来代表任意文本。这句话表示，若传入的 URI 能够匹配“content://introdcuton.android.myprovider/text/ 数字”格式，则返回匹配码 2。