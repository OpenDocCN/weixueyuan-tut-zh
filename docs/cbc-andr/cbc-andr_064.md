# Android 自定义 ContentProvider

> 原文：[`c.biancheng.net/view/3123.html`](http://c.biancheng.net/view/3123.html)

Android 系统支持任意应用程序创建自己的 ContentProvider，以便于将应用程序的数据对其他应用程序共享。

创建应用程序自己的 ContentProvider 需要以下几个步骤：

**1）**当前应用程序必须具有自己的持久化数据，例如文件存储或者使用 SQLite 数据库存储。

**2）**当前应用程序需要实现 ContentProvider 的子类，并通过该子类完成对持久化数据的访问。

**3）**在 AndroidManifest.xml 文件中使用 <provider> 标签声明当前应用程序定义的 ContentProvider。此外，还可以在 AndroidManifest.xml 文件中指定相应的访问权限，以保证该 ContentProvider 仅被具有相应权限的应用程序访问。若不指定访问权限，则任意其他应用程序都可以访问该 ContentProvider。

在实际的应用中，为了方便应用程序所定义的 ContentProvider 被其他应用程序使用，通常会定义一个类，将 ContentProvider 相关信息以静态常量的方式放置到该类中。这样，使用该 ContentProvider 的应用程序只要将该类引用进来，就可以获取该 ContentProvider 的相关信息，进而通过其对数据进行操作。

本节以 Android SQLite 数据库操作实例教程中使用的实例 MyDbDemo 为例，为该实例中创建的 SQLite 数据库 mydb 中的 friends 数据表创建 ContentProvider，以便于其他应用程序通过该 ContentProvider 对 friends 数据表中的数据进行访问。

在实例 MyDbDemo 中的 introduction.android.mydbDemo 包下创建两个文件，分别为 MyDbProvider.java 和 MyFriendsDB.java。

MyDbProvider 继承了 ContentProvider 类，实现了针对 mydb 的 friends 数据表的相关操作。MyFriendsDB 中包含涉及 MyDbProvider 的相关信息。

MyDbProvider.java 的代码如下：

```

package introduction.android.mydbdemo;

import android.content.ContentProvider;
import android.content.ContentUris;
import android.content.ContentValues;
import android.content.UriMatcher;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteQueryBuilder;
import android.net.Uri;
import android.util.Log;

public class MyDbProvider extends ContentProvider {
    private dbHelper mydbHelper;
    private static final UriMatcher myUriMatcher;

    static {
        myUriMatcher = new UriMatcher(UriMatcher.NO_MATCH);
        myUriMatcher.addURI(MyFriendsDB.AUTHORITY, "friends", MyFriendsDB.FRIENDS);
        myUriMatcher.addURI(MyFriendsDB.AUTHORITY, "friends/#", MyFriendsDB.FRIENDS_ID);
    }
    @Override
    public int delete(Uri uri, String selection, String[] selectionArgs) {
        // TODO Auto-generated method stub
        if (myUriMatcher.match(uri) != MyFriendsDB.FRIENDS_ID) {
            throw new IllegalArgumentException("Wrong Insert Type: " + uri);
        }
        String id = uri.getPathSegments().get(1);
        if (selection == null)
            selection = MyFriendsDB.ID + "=" + id;
        else
            selection = MyFriendsDB.ID + "=" + id + " and " + selection;
            SQLiteDatabase db = mydbHelper.getWritableDatabase();
            int i = db.delete(dbHelper.TB_NAME, selection, selectionArgs);
            if (i > 0)
                Log.i("myDbDemo", "数据更新成功！");
            else
                Log.i("myDbDemo", "数据未更新!");
        return i;
    }

    @Override
    public String getType(Uri uri) {
        // TODO Auto-generated method stub
        switch (myUriMatcher.match(uri)) {
            case MyFriendsDB.FRIENDS:
                return MyFriendsDB.CONTENT_TYPE;
            case MyFriendsDB.FRIENDS_ID:
                return MyFriendsDB.CONTENT_ITEM_TYPE;
            default:
                hrow new IllegalArgumentException("Unknown URI get type: " + uri);
        }
    }

    @Override
    public Uri insert(Uri uri, ContentValues values) {
        // TODO Auto-generated method stub
        if (myUriMatcher.match(uri) != MyFriendsDB.FRIENDS) {
            throw new IllegalArgumentException("Wrong Insert Type: " + uri);
        }
        if (values == null) {
            throw new IllegalArgumentException("Wrong Data.");
        }
        SQLiteDatabase db = mydbHelper.getWritableDatabase();
        long rowId = db.insert(MyFriendsDB.TABLE_NAME, null, values);
        if (rowId > 0) {
            Uri insertUri = ContentUris.withAppendedId(MyFriendsDB.CONTENT_URI, rowId);
            return insertUri;
        }
        return null;
    }

    @Override
    public boolean onCreate() {
        // TODO Auto-generated method stub
        mydbHelper = new dbHelper(getContext(),MyFriendsDB.DATABASE_NAME, null, MyFriendsDB.DATABASE_VERSI0N);
        return false;
    }

    @Override
    public Cursor query(Uri uri, String[] projection, String selection,String[] selectionArgs, String sortOrder) {
        // TODO Auto-generated method stub
        switch (myUriMatcher.match(uri)) {
            case MyFriendsDB.FRIENDS:
            break;
            case MyFriendsDB.FRIENDS_ID:
            Log.d("MyDbProvider", "select id");
            String id = uri.getPathSegments().get(1);
            if (selection == null)
                selection = MyFriendsDB.ID + "=" + id;
            else 
              selection = MyFriendsDB.ID + "=" + id + " and " + selection;
              break;
        default:
          throw new IllegalArgumentException("Unknown URI type: " + uri);
        }

        if (sortOrder == null)
            sortOrder = "_id ASC";
            SQLiteDatabase db = mydbHelper.getReadableDatabase();
            Cursor c = db.query(MyFriendsDB.TABLE_NAME, projection, selection, selectionArgs,null,null, sortOrder);
            Log.d("MyDbProvider", "" + c.getCount());
            return c;
    }

    @Override
    public int update(Uri uri, ContentValues values, String selection,String[] selectionArgs) {
        // TODO Auto-generated method stub
        if (myUriMatcher.match(uri) != MyFriendsDB.FRIENDS_ID) {
            throw new IllegalArgumentException("Wrong Insert Type: " + uri);
        }
        if (values == null) {
            throw new IllegalArgumentException("Wrong Data.");
        }
        String id = uri.getPathSegments().get(1);
        if (selection == null)
            selection = MyFriendsDB.ID + "=" + id;
        else
            selection = MyFriendsDB.ID + "=" + id + " and " + selection;
            SQLiteDatabase db = mydbHelper.getWritableDatabase();
            int i = db.update(dbHelper.TB_NAME, values, selection, selectionArgs);
            if (i > 0)
                Log.i("myDbDemo ", "数据更新成功! ");
            else
                Log.i("myDbDemo", "数据未更新!");
                return i;
    }
}

```

MyFriendsDB.java 的代码如下：

```

package introduction.android.mydbdemo;

import android.net.Uri;

public class MyFriendsDB {

    public static final String AUTHORITY = "introduction.android.mydbdemo.myfriendsdb";
    public static final String DATABASE_NAME = "mydb";
    public static final int DATABASE_VERSI0N = 1;
    public static final String TABLE_NAME = "friends";

    public static final Uri CONTENT_URI = Uri.parse("content://" + AUTHORITY + "/friends");
    public static final int FRIENDS = 1;
    public static final int FRIENDS_ID = 2;

    public static final String CONTENT_TYPE = "vnd.android.cursor.dir/mydb.friends.all";
    public static final String CONTENT_ITEM_TYPE = "vnd.android.cursor.dir/mydb.friends.item";
    public static final String ID = "_id";

    public static final String NAME = "name";
    public static final String AGE = "age";
}
```

这样，就定义了针对 mydb 的 friends 数据表的 ContentProvider，最后需要在 AndroidManifest.xml 文件中添加该 ContentProvider 的相应声明和访问权限。AndroidManifest.xml 的代码如下：

```

<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="introduction.android.mydbdemo"
    android:versionCode="1"
    android:versionName="1.0">

     uses-sdk android:minSdkVersion="14" />
     uses-permission android:name="introduction.android.permission.USE_MYDB" />
    application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        provider
            android:name="MyDbProvider"
            android:authorities="introduction.android.mydbdemo.MyFriendsDB" />
        activity android:name=".MainActivity">
            <intent-filter>
                action android:name="android.intent.action.MAIN" />
                category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        /activity>
    /application>

</manifest>
```

该文件通过如下代码：

<provider android:name="MyDbProvider" android:authorities="introduction.android.mydbdemo.MyFriendsDB" />

指明该 ContentProvider 名为 MyDbProvider，该 ContentProvider 的 Authority 为 introduction.android.mydbdemo.myfriendsdb。

通过如下代码：

<uses-permission android:name="introduction.android.permission.USE_MYDB"/>

指明该 ContentProvider 的权限为 introduction.android.permission.USE_MYDB，只有具有该权限的应用程序才可以访问该 ContentProvider。