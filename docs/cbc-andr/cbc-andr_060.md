# Android 使用 SQLite 数据库存数数据

> 原文：[`c.biancheng.net/view/3106.html`](http://c.biancheng.net/view/3106.html)

前面我们介绍了用 SharedPreferences 和文件存储信息的方法，但是当频繁大量地使用数据存储时，就要用到数据库来管理信息数据。

在 Android 中，我们使用 SQLite 数据库，在应用中也常常会用到 SQLite 来存储、管理、维护数据，下面将详细介绍 SQLite 的使用方法。

## SQLite 数据库简介

Android 通过 SQLite 数据库引擎来实现结构化数据存储。

Android 系统提供对 SQLite 数据库的完全支持，在数据库应用程序中，任何类都可以通过名字对已创建的数据库进行访问，但是在应用程序之外不可以。

SQLite 是一个轻量级数据库，第一个版本诞生在 2000 年 5 月，其遵守 ACID 的关联式数据库管理系统，最初就是为嵌入式设计的，其占用资源非常少，在内存中只需要占用几百千字节（KB）的存储空间，这也是 Android 采用 SQLite 数据库的重要原因之一。

同时，SQLite 还支持事务处理功能，根据相关资料可知，SQLite 的处理速度比 MySQL、PostgreSQL 等著名的开源数据库管理系统更快。另外，SQLite 数据库不像其他的数据库（如 Oracle），它没有服务器进程。

SQLite 通过文件保存数据库，该文件是跨平台的，可以自由复制。一个文件就是一个数据库，数据库名称即文件名；

数据库里面可以包含多个表格，在每个表格中可以添加多条记录，但记录没有名称；记录可以由多个字段组成，每个字段都要有相对应的值，每个值都必须指定类型。基于 SQLite 自身的先天优势，其在嵌入式领域中得到了广泛应用。

SQLite 支持 SQL 语言，由 SQL 编译器、内核、后端以及附件组成。

SQLite 通过利用虚拟机和虚拟数据库引擎（VDBE），使调试、修改和扩展 SQLite 的内核变得更加方便。

Android 在运行时（run-time）集成了 SQLite，所以每个 Android 应用程序都可以使用 SQLite 数据库，在 Android 开发中使用 SQLite 相当简单。

由于 JDBC 会消耗太多的系统资源，所以 JDBC 对于手机这种内存受限的设备来说并不合适。因此，Android 提供了一些新的 API 来使用 SQLite 数据库。

在 Android 开发中，我们需要学习使用这些 API。另外需要了解的是，数据库存储在 data/<项目文件夹>/databases/ 目录下。

操作 SQLite 数据的步骤如下：

#### 1）创建 SQLite 数据库。

Android 系统推荐的创建 SQLite 数据库的方法是创建实现 SQLiteOpenHelper 接口的子类，并且重写 onCreate() 方法，在该方法中执行用于创建 SQLite 数据库的命令。

所创建的数据库被放置在 /data/data/<your package name>/database 目录下，例如：

```

public class DictionaryOpenHelper extends SQLiteOpenHelper {

    private static final int DATABASE_VERSION = 2;
    private static final String DICTIONARY_TABLE_NAME = "dictionary";
    //创建数据库的 SQL 语句
    private static final String DICTIONARY_TABLE_CREATE =
        "CREATE TABLE " + DICTIONARY_TABLE_NAME + "(" + KEY_WORD + " TEXT, " +
        KEY_DEFINITION + "TEXT);";
    DictionaryOpenHelper(Context context) {
        super(context, DATABASE_NAME, null, DATABASE_VERSION);
    }
    @Override
    public void onCreate(SQLiteDatabase db) {
        db.execSQL(DICTIONARY_TABLE_CREATE);//执行 SQL 语句
    }
}
```

#### 2）获取数据库对象

通过实现 SQLiteOpenHelper 接口的类的对象，调用 getWritableDatabase() 和 getReadableDatabase() 方法，可以返回所创建数据库的 SQLiteDatabase 对象。

#### 3）对数据库进行操作

SQLiteDatabase 对象提供了对数据库进行操作的一系列方法，例如 query、insert、delete、update 等，进而对 SQLite 数据库进行读写等操作。

#### 4）返回结果

对数据库的查询操作会返回一个 Cursor 对象，通过该对象可以从返回的结果中读取出行、列信息。

## SQLite 数据库操作

Android 提供了创建和使用 SQLite 数据库的 API。Android SDK 提供了一系列对 SQLite 数据库进行操作的类和接口，这里我们简单介绍一下。

#### 1）SQLiteDatabase 类

SQLiteDatabase 是一个数据库访问类，此类封装了一系列数据库操作的 API，使其可以对数据进行 CRUD 操作，即添加、查询、更新、删除等。一些常用的操作数据库的方法如下表所示。

**SQLiteDatabase 常用方法**

| 方法名称 | 方法描述 |
| --- | --- |
| openOrCreateDatabase (Stringpath,SQLiteDatabase.CursorFactory factory) | 打开或创建数据库 |
| insert (String table,String nullColumnHack,Content Values values) | 添加一条记录 |
| delete (String table,String whereClause,String[] whereArgs) | 删除一条记录 |
| query (String table, String[] columns, String selection, String[] selectionArgs, String groupBy,String having,String orderBy) | 查询一条记录 |
| update (String table,ContentValues values,String whereClause,String[] whereArgs) | 修改记录 |
| execSQL (String sql) | 执行一条 SQL 语句 |
| close() | 关闭数据库 |

#### 2）SQLiteOpenHelper 类

SQLiteOpenHelper 是一个抽象类，用来创建和版本更新。

SQLiteOpenHelper 的子类通过 getReadableDatabase() 和 getWritableDatabase() 方法来获取 SQLiteDatabase 实例对象，并保证以同步方式访问。

通常情况下，getReadableDatabase() 和 getWritableDatabase() 都是创建或者打开一个可写数据库，并返回相同的对象。

只有在某些情况下，例如磁盘空间满了，或者数据库只能以只读方式打开的时候，getReadableDatabase() 方法才会以查询方式打开数据库。

其一般的用法是定义一个类继承之，并实现其抽象方法来创建和更新数据库，其常见的方法如下表所示。

**SQLiteOpenHelper 常用方法**

| 方法名称 | 方法描述 |
| --- | --- |
| SQLiteOpenHelper ( Context context,String name, CursorFactory factory, int version) | 构造方法，一般是要传递一个要创建的数据库名称 |
| onCreate (SQLiteDatabase db) | 创建数据库时调用 |
| onUpdate ( SQLiteDatabase db,int oldVersion,int new Version) | 版本更新时调用 |
| getReadableDatabase() | 创建或打开一个只读数据库 |
| getWritableDatabase() | 创建或打开一个读写数据库 |

#### 3）Cursor 接口

Cursor 是一个游标接口，在数据库中使用时作为返回值，相当于结果集 ResultSet。它提供了遍历查询结果的方法。Cursor 游标的一些常用方法如下表所示。

**Cursor 游标常用方法**

| 方法名称 | 方法描述 |
| --- | --- |
| close() | 关闭游标，释放资源 |
| copyStringToBuffer(int columnlndex, CharArrayBuffer buffer) | 在缓冲区中检索请求的列的文本，并将其存储 |
| getColumnCount() | 返回所有列的总数 |
| getColumnIndex( String columnName) | 返回指定列的名称，如果不存在，就返回-1 |
| getColumnIndexOrThrow(String columnN ame) | 从零开始返回指定列的名称，如果不存在，将抛出异常 |
| getColumnName(int columnlndex) | 从给定的索引返回列名 |
| getColumnNames() | 返回一个字符串数组的列名 |
| getCount() | 返回 Cursor 中的行数 |
| moveToFirst() | 移动光标到第一行 |
| moveToLast() | 移动光标到最后一行 |
| moveToNext() | 移动光标到下一行 |
| moveToPosition(int position) | 移动光标到一个绝对的位置 |
| moveToPreviousQ | 移动光标到上一行 |

这些方法的使用可以通过创建数据库、创建表和执行 SQL 语句的过程一一进行介绍。

**1）打开或创建数据库**

openOrCreateDatabase() 方法会自动检测要打开的数据库是否存在，如果存在就打开，否则创建一个数据库。

若该方法运行成功，则返回一个 SQLiteDatabase 对象，否则抛出异常 FileNotFoundException。下面为创建名为“sie.db”的数据库的代码：

SQLiteDatabase database=SQLiteDatabase.openOrCreateDatabase("/data/data/sie.db",null);

**2）创建数据表**

使用 SQLiteDatabase 的 execSQL() 方法执行 SQL 语句，便能创建一个表。

下面创建一个表，其中有三个属性：_id 为主键并自动增加，name 为姓名，number 为编号，相关代码如下：

String table="create table sietexttable(_id integer primary key autoincrement,name text, number text)";
database.execSQL(table);

**3）插入数据**

使用 SQLiteDatabase 的 insert(String table,String nullColumnHack, ContentValues values) 方法。

参数说明：

*   第一个参数是表名称。
*   第二个参数是空列的默认值。
*   第三个参数是 ContentValues 封装的列的名称和对应的列值。代码如下：

```

ContentValues values=new ContentValues();
values.put("name", "sietext01");
values.put("number", "001");
database.insert("table", null, values);
```

**4）删除数据**

使用 SQLiteDatabase 的 delete(String table,String whereClause,String[] whereArgs) 方法。

参数说明：

*   第一个参数是表的名称。
*   第二个参数是删除条件。
*   第三个参数是条件值数组。

**5）修改数据**

使用 SQLiteDatabase 的 update(String table,ContentValues values,String whereClause, String[] whereArgs) 方法。

参数名称：

*   第一个参数是表名称。
*   第二个参数是更新行和列的 ContentValues 类型的键值对。
*   第三个参数是更新条件。
*   第四个参数是更新的条件数组。

此外，上述三种能够引起数据库数据改变的操作，都可以通过 SQLiteDatabase 的 execSQL() 方法来完成。

**6）查询数据**

在 Android 中通过 Cursor 类来实现，使用 SQLiteDatabase.query() 方法会得到一个 Cursor 对象。

Cursor 用于指向查询结果中的记录。下面使用 Cursor 查询数据库中的数据，代码如下：

```

Cursor cursor = database.query("table",null,null,null,null,null,null);
/*判断游标是否为空*/
if(cursor.moveToFirst()){
    for (int i = 0;i<cursor.getCount();i++){
        cursor.moveToNext();
        //获得 ID
        int id = cursor.getInt(0);
        //获得用户名
        String name = cursor.getString(1);
        //获得编号
        String number = cursor.getString(2);
    }
}
```

SQLiteOpenHelper 类是 SQLiteDataBase 的帮助类，这个类主要用于打开或者创建数据库，并返回数据库对象，同时对数据库的版本进行管理。并且它是一个抽象类，需要继承它并实现里面的两个抽象方法：

*   onCreate(SQLiteDatabase)：在数据库第一次生成的时候会调用这个方法，一般在这个方法中生成数据库表。
*   onUpgrade(SQLiteDatabase,int,int)：当数据库需要升级的时候，系统会主动调用这个方法。一般在这个方法中删除原有数据表，并建立新的数据表。