# Android 复制粘贴开发教程

> 原文：[`c.biancheng.net/view/3319.html`](http://c.biancheng.net/view/3319.html)

前面《Android 剪贴板框架简介》和《Android 剪贴板数据转文本简介》教程我们讲了如果要把数据复制到剪贴板（剪贴板句柄指向全局的 ClipboardManager 对象）上，需要创建一个 ClipData 对象，再把一个 ClipDescription 和一个以上的 ClipData.Item 对象加入其中，最后把这个 ClipData 添加到 ClipboardManager 对象中去。

而要从剪贴板粘贴数据，需先获得全局剪贴板对象，再获取 clip 对象，查找其中的数据，最后从 clip 对象中把数据复制到自己的存储中。

本节将详细描述把数据复制到剪切板的具体实现过程和如何针对三种剪贴板数据格式进行从剪切板粘贴数据。

## 复制

具体的实现过程如下：

要复制 Content URI 类型的数据，先要建立一个 Content Provider。

NotePad 是一个使用 Content Provider 复制粘贴数据的示例。NotePadProvider 类实现了 ContentProvider。NotePad 类定义了该 Provider 和其他应用程序的交互方式，包括支持所用的 MIME 类型。

#### 1）获取系统剪贴板。

其代码如下：

```

...
//if the user selects copy
case R.id.menu_copy:

//Gets a handle to the clipboard service.
ClipboardManager clipboard = (ClipboardManager)getSystemService(Context.CLIPNOARD_SERVICE);
```

#### 2）复制数据给一个 ClipData 对象。

**①文本**

如果数据是一个文本，代码如下：

// Creates a new text clip to put on the clipboard
ClipData clip = ClipData.newPlainText("simple text","Hello, World!");

**② URI**

如果数据是一个 URI 对象，把记录 ID 编入 Provider 用到的 Content URI 中的代码如下：

```

// Creates a Uri based on a base Uri and a record ID based on the contact's last name
// Declares the base URI string

private static final String CONTACTS = "content://com.example.contacts";

// Declares a path string for URIs that you use to copy data
private static final String COPY_PATH = "/copy";

// Declares the Uri to paste to the clipboard
Uri copyUri = Uri.parse(CONTACTS + COPY_PATH + "/" + lastName);
...
// Creates a new URI clip object. The system uses the anonymous getContentResolver() object to
// get MIME types from provider. The clip object's label is "URI", and its data is
// the Uri previously created.

ClipData clip = ClipData.newUri(getContentResolver(),"URI",copyUri);
```

**③ Intent**

如果数据是一个 Intent 对象，将其放入 clip 对象中的代码如下：

```

//Creates the Intent
Intent appIntent = new Intent(this,com.example.demo.myapplication.class);
...
//Creates a clip object with the Intent in it.Its label is "Intent" and its data is
//the Intent object created previously
ClipData clip = ClipData.newIntent("Intent",appIntent);
```

把新建的 clip 对象放入剪贴板的代码如下：

// Set the clipboard's primary clip.
clipboard.setPrimaryClip(clip);

## 粘贴

#### 1．粘贴普通文本

要粘贴普通文本，首先获得全局剪贴板，并确认能否返回普通文本，然后获取 clip 对象，用 getText() 把其中的文本复制到自己的存储中。实现步骤如下：

**1）**用 getSystemService(CLIPBOARD_SERVICE) 获得全局 ClipboardManager 对象，并声明一个全局变量，用来存放粘贴到的文本。

ClipboardManager clipboard = (ClipboardManager)getSystemService(Context.CLIPBOARD_SERVICE);
String pasteData="";

**2）**确定启用或禁用当前 Activity 的“粘贴”选项，并验证剪贴板中是否包含 clip，且程序是否有能力处理其数据类型。

```

// Gets the ID of the "paste" menu item
MenuItem mPasteItem = menu.findItem(R.id.menu_paste);

// If the clipboard doesn't contain data, disable the paste menu item.
// If it does contain data, decide if you can handle the data.
if (!(clipboard.hasPrimaryClip())) {
    mPasteItem.setEnabled(false);
} else if (!(clipboard.getPrimaryClipDescription().hasMimeType(MIMETYPE_TEXT_PLAIN))) {

    // This disables the paste menu item, since the clipboard has data but it is not plain text
    mPasteItem.setEnabled(false);
} else {
    // This enables the paste menu item, since the clipboard contains plain text.
    mPasteltem.setEnabled(true);
}
```

**3）**从剪贴板中复制数据。只有“粘贴”菜单项启用时，程序才会运行至此，所以这时可以假定剪贴板已经包含普通文本，不过还不清楚里面包含文本字符串还是指向普通文本的 URI。测试处理普通文本的部分代码如下：

```

// Responds to the user selecting "paste"

case R.id.menu_paste:

// Examines the item on the clipboard. If getTextO does not return null, the clip item contains the
// text. Assumes that this application can only handle one item at a time.
ClipData.Item item = clipboard.getPrimaryClip().getItemAt(0);

// Gets the clipboard as text.
pasteData = item.getText();

// If the string contains data, then the paste operation is done
if (pasteData != null) {
    return;

// The clipboard does not contain text. If it contains a URI, attempts to get data from it
} else {
    Uri pasteUri = item.getUri();

    // If the URI contains something, try to get text from it
    if (pasteUri != null) {

        // calls a routine to resolve the URI and get data from it. This routine is not
        // presented here. pasteData = resolveUri(Uri);
        return;
    } else {
        // Something is wrong. The MIME type was plain text, but the clipboard does not contain either
        // text or a Uri. Report an error.
        Log.e("Clipboard contains an invalid data type");
        return;
    }
}
```

#### 2．从 Content URI 粘贴数据

如果 ClipData.Item 对象包含一个 Content URI，程序也能处理其中的 MIME 类型，可创建一个 ContentResolver 对象，并调用 Content Provider 的相关方法来获取数据。

以下过程描述了如何根据剪贴板中的 Content URI 从 Content Provider 获取数据。

**1）声明全局变量，用于存放 MIME 类型。**

程序先检查 MIME 类型，确认能够使用 Provider 提供的数据。

// Declares a MIME type constant to match against the MIME types offered by the provider
public static final String MIME_TYPE_CONTACT = "vnd.android.cursor.item/vnd.example.contact"

**2）获取全局剪贴板，创建一个用于访问 Content Provider 的 Content Resolver 对象。**

```

//Gets a handle to the Clipboard Manager
ClipboardManager clipboard = (ClipboardManager)getSystemService(Context.CLIPBOARD_SERVICE);

//Gets a content resolver instance
ContentResolver cr = getContentResolver();
```

**3）从剪贴板获取主 clip，并把内容解析为 URI。**

```

// Gets the clipboard data from the clipboard
ClipData clip = clipboard.getPrimaryClip();

if (clip != null) {

    // Gets the first item from the clipboard data
    ClipData.Item item = clip.getltemAt(0);

    // Tries to get the item's contents as a URI
    Uri pasteUri = item.getUri();
```

**4）通过调用 getType(Uri)，判断 URI 是否为 Content URI。如果 URI 未指向合法的 Content Provider，该方法返回 null。**

```

//If the clipboard contains a URI reference
if(pasteUri != null){
    Is this a content URI?
    String uriMimeType = cr.getType(pasteUri);
}
```

**5）判断 Content Provider 是否支持应用程序识别的 MIME 类型。若支持，则调用 ContentResolver.query() 来获取数据，返回值是一个 Cursor 对象。**

```

// If the return value is not null, the Uri is a content Uri
if (uriMimeType != null) {
    // Does the content provider offer a MIME type that the current application can use
    if (uriMimeType.equals(MIME_TYPE_CONTACT)) {

        // Get the data from the content provider.
        Cursor pasteCursor = cr.query(uri, null, null, null, null);

        // If the Cursor contains data, move to the first record
        if (pasteCursor != null) {
            if (pasteCursor.moveToFirst()) {
                // get the data from the Cursor here. The code will vary according to the
                // format of the data model.
            }
        }
        // close the Cursor
        pasteCursor.close();
        }
    }
}
```

#### 3．粘贴 Intent

要粘贴一个 Intent，首先获取全局剪贴板，再检查 ClipData.Item 对象是否包含 Intent。最后调用 getIntent() 把 Intent 复制到程序的存储中。

实现代码如下：

```

// Gets a handle to the Clipboard Manager
ClipboardManager clipboard = (ClipboardManager)getSystemService(Context.CLIPBOARD_SERVICE);

// Checks to see if the clip item contains an Intent, by testing to see if getlntentO returns null
Intent pasteIntent = clipboard.getPriraaryClip().getItemAt(0).getIntent();

if (pasteIntent != null) {
    // handle the Intent
} else {
    // ignore the clipboard, or issue an error if your application was expecting an Intent to be
    // on the clipboard
}
```