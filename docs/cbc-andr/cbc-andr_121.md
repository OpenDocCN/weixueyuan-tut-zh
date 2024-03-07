# Android Content Provider 复制复杂数据开发教程

> 原文：[`c.biancheng.net/view/3324.html`](http://c.biancheng.net/view/3324.html)

Content Provider 支持对复杂数据的复制，比如数据库记录或文件流等。在复制数据时，把一个 Content URI 放入剪贴板中，然后粘贴应用程序，从剪贴板中获取该 URI，并用它读取数据库数据或者文件流的描述符。

粘贴应用程序只是将 Content URI 作为数据读取，并不清楚应该获取数据的哪部分，要实现其功能，可以把所需数据的 ID 编入 URI 本身，或者让 URI 精确返回所需复制部分的数据，而采用哪种方式取决于数据的组织形式。

#### 1．将 ID 置入 URI 编码

利用 URI 把数据复制到剪贴板时，可以把数据的 ID 置入 URI 编码本身，Content Provider 得到该 ID，利用 ID 读取数据。

粘贴应用程序无须判断 ID 是否存在，只需从剪贴板获取“引用”（URI 加 ID）交给 Content Provider，然后读取数据。

通常的编码方式是把 ID 附在 Content URI 后面。假设定义的 Provider URI 字符串如下：

"content://com.example.contacts"

如果需要把名称置入 URI，使用如下代码：

```

String uriString = "content://com.example.contacts"+"/"+"Smith";
//uriString now contains content://com.example.contacts/Smith

//Generates a uri object from the string representation
Uri copyUri = Uri.parse(uriString);
```

如果程序中已经使用了 Content Provider，只需新增一个指示复制数据的 URI 路径。假设已存在以下 URI 路径：

```

"content://com.example.contacts"/people
"content://com.example.contacts"/people/detail
"content://com.example.contacts"/people/images
```

下面加入一个用于复制的 URI：

"content://com.example.contacts/copying"

可以利用模式匹配来检测“copy” URI，并用代码进行复制和粘贴处理。

如果是用 Content Provider、内部数据库、内部表来组织数据，通常可以使用以上编码技术。这种情况下会有多块数据需要复制，很可能每块数据都会有一个唯一的 ID。

当粘贴应用程序查询时，可以用此 ID 查找并返回数据。如果没有多块数据需要复制，就不必把 ID 进行编码，简单地使用能够唯一标识 Provider 的 URI 即可。查询时，Provider 会返回包含的数据。

Note Pad 示例中就用 ID 获取了单条记录，以便从 note 列表中打开一条 note。此示例使用了 SQL 数据库中的 _id 字段，也可以根据需要使用任何数字或字符 ID。

#### 2．复制数据结构

为了复制和粘贴复杂数据，需要创建一个 ContentProvider 组件的子类 Content Provider，将编码后的 URI 放入剪贴板，该 URI 指向需提供的正确记录。此外，还必须考虑应用程序的现状：

*   如果已有一个 Content Provider，只需要扩展它的功能，修改 query() 方法，使得它能处理粘贴程序所需的 URI 即可，也可以修改方法来对 URI 中的“copy”进行模式匹配。
*   如果应用程序拥有内部数据库，为了复制数据，需要将此数据库移入 Content Provider。
*   如果没有用到数据库，可以实现一个简单的 Content Provider，其为程序提供来自剪贴板数据的粘贴功能。

在 Content Provider 中，需要重写 query() 和 getType() 两个方法。

**1）query()**

假设粘贴应用程序通过该方法获取剪贴板中 URI 指定的数据，为了支持复制功能，应该在本方法中对包含指定“copy”路径的 URI 进行检测。然后，程序可以创建一个“copy”URI，并放入剪贴板中，此 URI 包含复制路径和指向实际复制记录的指针。

**2）getType()**

本方法返回 MIME 类型或者需复制数据的类型。为了把 MIME 类型放入新建的 ClipData 对象中，newUri() 方法将会调用 getType() 方法。

注：insert() 或 update() 等其他的 Content Provider 方法不需要实现。粘贴应用程序只需要获取所用的 MIME 类型并从 Provider 复制数据。如果已经实现了这些方法，它们也不会影响复制操作。

下面演示如何实现复制复杂数据的应用程序。

① 声明全局常量，定义基本 URI 字符串和路径，用于指明复制数据的 URI 字符串。同时，声明复制数据的 MIME 类型。

```

//Declares the base URI string
private static final String CONTACTS="content://com.example.contacts";

//Declares a path string for URIs that you use to copy data
private static final String COPY_PATH = "/copy";

//Declares a MIME type for the copied data
public static final MIME_TYPE_CONTACT = "vnd.android.cursor.item/vnd.example.contact";
```

② 在用户复制数据的 Activity 中，把数据复制到剪贴板，在响应复制请求时，将 URI 放入剪贴板中的代码如下。

```

public class MyCopyActivity extends Activity {
    ...

    // The user has selected a name and is requesting a copy.

    case R.id.menu_copy:

        // Appends the last name to the base URI
        // The name is stored in "lastName"
        uriString = CONTACTS + COPY_PATH + "/" + lastName;

        // Parses the string into a URI
        Uri copyUri = Uri.parse(uriString);

        // Gets a handle to the clipboard service.
        ClipboardManager clipboard = (ClipboardManager) getSystemService(Context.CLIPBOARD_SERVICE);

        ClipData clip = ClipData.newUri(getContentResolver(), "URI", copyUri);

        // Set the clipboard's primary clip. clipboard.setPrimaryClip(clip);
```

③ 在 Content Provider 的全局部分（global scope），创建一个 URI 匹配器，并加入与剪贴板 URI 相匹配的 URI 模式，代码如下：

```

public class MyCopyProvider extends ContentProvider {
    ...
    // A Uri Match object that simplifies matching content URIs to patterns.
    private static final UriMatcher sURIMatcher = new UriMatcher(UriMatcher.NO_MATCH);

    // An integer to use in switching based on the incoming URI pattern
    private static final int GET_SINGLE_CONTACT = 0;

    ...

    // Adds a matcher for the content URI. It matches
    // "content://com.example.contacts/copy/*"

    sUriMatcher.addURI(CONTACTS, "names/*", GET_SINGLE_CONTACT);
```

④ 实现 query() 方法。在本方法中可用不同的代码处理各种 URI 模式，下面的代码仅列出了剪贴板复制操作所用到的模式。

```

// Sets up your provider's query() method.

public Cursor query(Uri uri, String[] projection, String selection, String[] selectionArgs, String sortOrder) {

...

// Switch based on the incoming content URI
switch (sUriMatcher.match(uri)) {

    case GET_SINGLE_CONTACT:
        // query and return the contact for the requested name. Here you would decode
        // the incoming URI, query the data model based on the last name, and return the result
        // as a Cursor.

    ...

}
```

⑤ 实现 getType() 方法，返回复制数据的 MIME 类型。

```

// Sets up your provider's getType() method.
public String getType(Uri uri) {
    ...
    switch (sUriMatcher.match(uri)) {
    case GET_SINGLE_CONTACT:
        return (MIME TYPE CONTACT);
```

本部分描述了如何从剪贴板获取数据的 URI，并通过 URI 进行数据的读取和粘贴。

#### 3\. 复制数据流

大量的文本和二进制数据可以以流的形式进行复制和粘贴。这样的数据具有如下特性：

保存在物理设备上的文件。
来自 Socket 的流。
保存在 Provider 底层数据库系统中的大量数据。

数据流的 Content Provider 用诸如 AssetFileDescriptor 的文件描述符对象代替 Cursor 对象，粘贴应用程序利用该文件描述符来读取数据流。

要创建 Provider 复制数据流的应用程序，遵循以下步骤：

**1）为放入剪贴板的数据流建立 Content URI。**可以通过以下三种方式实现。

*   将数据流的 ID 编入 URI，如上将 ID 编入 URI 所述，然后在 Provider 中保存一张表，其中包含 ID 和相关的流名称。
*   将流名称直接编入 URI。
*   使用从 Provider 返回到当前流的唯一 URI。若选用该方式，则每次通过 URI 把流复制到剪贴板时，必须更新 Provider，使它指向新的流。

**2）为每类提供的数据流指定一个 MIME 类型。**粘贴应用程序需要此信息来确定能否粘贴剪贴板中的数据。

**3）实现 ContentProvider 中的一个方法，返回流的文件描述符。**若 ID 已编入 Content URI，则用此方法来确定需要打开的流。

**4）数据复制到剪贴板时，构造 Content URI 并放入剪贴板中。**

粘贴数据流时，应用程序先从剪贴板获取 clip，读取 URI，然后调用 ContentResolver 文件描述符方法打开流。

ContentResolver 方法将调用相应的 ContentProvider 方法，把 Content URI 传入其中。Provider 把文件描述符返回给 ContentResolver 方法，这时粘贴程序就能读取流中的数据了。

以下给出了 Content Provider 中重要的文件描述符方法。每个方法名末端都增加字符串 Descriptor 的 ContentResolver 方法与之相对应。比如，模拟 openAssetFile() 的 ContentResolver 方法是 openAssetFileDescriptor()。

**① openTypedAssetFile()**

仅当给出的 MIME 类型能被 Provider 支持时，本方法返回一个 asset 文件描述符。调用方（执行粘贴的应用）提供 MIME 类型模式。如果能提供此类型 MIME，Content Provider（把 URI 复制到剪贴板的应用）将返回一个 AssetFileDescriptor 文件句柄，不能提供则抛出异常。

本方法用于处理文件的片段，可以用它读取 Content Provider 复制到剪贴板的 asset。

**② openAssetFile()**

本方法是比 openTypedAssetFile() 更通用的方法。它不对支持的 MIME 类型进行判断过滤，但可用于读取文件的片段。

**③ openFile()**

这是比 openAssetFile() 更加通用的格式，但它不能读取文件片段。

**④ openPipeHelper()**

可以选用 openPipeHelper() 方法作为文件描述符方法，让粘贴应用可以利用管道在后台读取流数据。使用此方法需要实现 ContentProvider.PipeDataWriter 接口。

在 Note Pad 示例程序中有相关用法，其使用位于 NotePadProvider.java 中的 openTypedAssetFile() 方法。