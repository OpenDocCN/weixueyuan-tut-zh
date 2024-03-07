# Android 剪贴板框架开发简介

> 原文：[`c.biancheng.net/view/3308.html`](http://c.biancheng.net/view/3308.html)

Android 7.0 提供的文本服务可以为应用程序添加诸如复制/粘贴、检查拼写等功能。用户也可以开发自己的文本服务，像其他应用程序一样分发给其他用户，文本服务具有自定义的输入法编辑器（IME）、字典和拼写检查器。

Android 为复制和粘贴提供了强大的、基于剪贴板的框架。它支持多种数据类型，包括文本字符串、复杂的数据结构、文本和二进制数据流，甚至其他复杂的应用类型。

简单的文本数据被保存在剪贴板中，而复杂的数据会保存成一个引用，粘贴应用程序会使用 Content Provider 解析该引用。复制和粘贴工作可以在应用程序内部进行，也可以在实现该框架的两个应用程序之间进行。

下面我们先介绍剪贴板框架

使用剪贴板框架（Clipboard Framework）时，把数据放在一个剪切对象（clip object）中，这个对象会自动放在系统的剪贴板中。

剪切对象有以下三种形式。

#### 1）Text

这种形式下，字符串被直接放在剪切对象中，然后放在剪贴板里。粘贴这个字符串的时候，直接从剪贴板取出这个对象，把字符串放入应用存储中。

#### 2）URI

可以表示任何形式的 URI。这种形式主要用于从一个 Content Provider 中复制复杂的数据。

复制的时候把一个 URI 对象放在一个剪切对象中，再放在剪贴板里。粘贴的时候取出这个剪切对象，得到 URI，把它解析为一个数据资源，比如 Content Provider，然后从资源中复制数据到应用存储中。

#### 3）Intent

它支持复制应用程序的快捷方式。要复制这种数据，就要创建一个 Intent 对象，把它放到一个剪切对象中，并将这个剪切对象放到系统剪贴板上。

粘贴数据时，要从剪贴板上获取这个剪切对象，然后把这个 Intent 对象放到应用程序的内存中。

系统剪贴板每次仅有一个剪切对象，当一个应用程序把一个剪切对象放到剪贴板上时，前一个剪切对象就会消失。

如果允许用户把数据粘贴到应用程序中，可以在粘贴之前检查剪贴板上的数据，而不必处理所有的数据类型。

剪切对象包含 MIME 类型或可用类型的元数据，这个元数据会帮助我们判断应用程序是否可以使用剪贴板上的数据。

例如，如果想要处理文本，可以忽略包含 URI 或 Intent 对象的剪切对象。如果让用户粘贴文本，而不管剪贴板上的数据格式，可以强制把剪贴板数据转换成文本形式，然后粘贴这个文本。

## 剪切板类

下面主要描述剪贴板框架中所使用的类。

#### 1．剪贴板管理器

在 Android 系统中，系统剪贴板由全局 ClipboardManager（剪贴板管理器）类表示。此类不需要直接初始化创建对象，而是通过 getSystemService(CLIPBOARD_SERVICE) 方法获取一个引用。

#### 2．ClipData、ClipData.Item 和 ClipDescription

要把数据加入剪贴板，可以创建一个 ClipData 对象，它包含数据描述信息和数据本身。剪贴板每次只保存一个 ClipData 对象，一个 ClipData 对象包含一个 ClipDescription 对象和一个以上的 ClipData.Item 对象。

**1）ClipDescription**

ClipDescription 对象包含 clip 相关的元数据信息。需要特别指出的是，它包含一个 clip 数据所对应 MIME 类型的数组。把 clip 放入剪贴板后，粘贴应用程序时可以利用此数组，程序可以检查该数组，以确定其对这些 MIME 类型的处理能力。

**2）ClipData.Item**

clipdata.Item 对象包含 Text、URI 和 Intent 数据。

**① Text**

文本，就是一个字符序列。

**② URI**

虽然可以取任何 URI 值，但通常包含一个 Content Provider URI。

提供数据的应用程序把 URI 放入剪贴板，需要粘贴数据的应用程序从剪贴板中获取 URI，并将它用于访问 Content Provider（或者其他数据源）并取回数据。

**③ Intent**

本数据类型允许把应用程序的快捷方式复制到剪贴板中，用户可以在后续的使用中把快捷方式粘贴到其他应用程序中。

在一个 clip 中可以包含一个或多个 Item 对象。这使得用户可以把多个选中的值复制到同一个 clip 中。比如，有一个列表允许用户一次选择多个选项，可以把所有选中的项一次复制到剪贴板中。要实现这一点，先为列表每一项创建一个 ClipData.Item 对象，再把这些 ClipData.Item 对象加入 ClipData 对象即可。

**3）ClipData 的简便方法**

ClipData 类为创建具有一个简单的 Clipdata.item 和 ClipDescription 对象的 ClipData 对象提供一些简便的静态方法。

**① newPlainText(label,text)**

该方法返回包含单个 ClipData.Item 对象的 ClipData 对象，该 item 对象内含一个文本字符串，ClipDescription 对象的标签设置为 label 时，ClipDescription 的 MIME 类型是 MIMETYPE_TEXT_ PLAIN。

newPlainText() 可用于创建一个文本字符串 clip。

**② newUri(resolver,label,URI)**

该方法也返回一个包含单个 ClipData.Item 的 ClipData 对象，该 item 对象内含一个 URI。其中，ClipDescription 对象的标签设置为 label。

若 URI 是 content 类型（Uri.getScheme() 返回 content:），则该方法将用 resolver 的 ContentResolver 对象从 Content Provider 中获取可用的 MIME 类型，并把这些类型保存到 ClipDescription 中。对于不是 content 的 URI ，该方法把 MIME 类型设置为 MIMETYPE_TEXT_URILIST。

newUri() 用于创建一个 URI 的 clip，特别是 content:URI。

**③ newIntent(label,intent)**

该方法也返回一个包含单个 ClipData.Item 的 ClipData 对象，该 item 对象内含一个 Intent。其中，ClipDescription 对象的标签设置为 label，MIME 类型置为 MIMETYPE_TEXT_INTENT。

newIntent() 用于创建一个 Intent 对象的剪贴对象。