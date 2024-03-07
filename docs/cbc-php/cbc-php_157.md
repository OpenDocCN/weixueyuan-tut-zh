# PHP fopen()和 fclose()：打开和关闭文件

> 原文：[`c.biancheng.net/view/7779.html`](http://c.biancheng.net/view/7779.html)

在操作文件之前，首先要打开文件才可以，这是进行文件操作的第一步，在 PHP 中要打开一个文件，可以使用 fopen() 函数，当打开一个文件的时候，还需要指定如何使用它，也就是文件模式。

有打开就有关闭，所以当文件操作完成之后，需要将打开的文件关闭以释放资源（fopen() 和 fclose() 函数通常是成对出现的），否则就会出现错误。

## 1、 选择文件模式

在对文件进行操作之前，服务器上的操作系统必须知道要对打开的文件进行什么操作。操作系统需要了解在打开这个文件后，这个文件是否还允许其他脚本再打开，它还需要了解使用者（或脚本）是否具有在这种方式下使用该文件的权限。

从本质上说，文件模式可以告诉操作系统一种机制，这种机制可以决定如何处理来自其他人或脚本的访问请求，以及一种用来检查你是否有权访问这个特定的文件。

当打开一个文件的时候，有以下 3 种选择：

*   打开文件为了只读、只写或者读和写；
*   如果要写一个文件，可以覆盖所有已有的文件内容，或者仅仅将新数据追加到文件末尾；
*   如果在一个区分了二进制方式和纯文本方式的系统上写一个文件，还必须指定采用的方式。

函数 fopen() 支持上述的 3 种方式的组合。

## 2、打开文件

PHP 中可以使用 fopen() 函数来打开文件或者 URL。如果打开成功，则返回文件指针资源；如果打开失败则返回 FALSE，该函数的语法格式如下所示：

fopen(string $filename, string $mode[, bool $use_include_path = false[, resource $context]])

参数说明如下：

*   $filename：为待打开文件的 URL，这个 URL 可以是文件所在服务器中的绝对路径，也可以是相对路径或者网络资源中的文件；
*   $mode：用来设置文件的打开方式（文件模式）。具体的值可以从下表中选取：

| mode | 说明 |
| r | 以只读方式打开，将文件指针指向文件头。 |
| r+ | 以读写方式打开，将文件指针指向文件头。 |
| w | 以写入方式打开，将文件指针指向文件头并将文件大小截为零。如果文件不存在则创建该文件。 |
| w+ | 以读写方式打开，将文件指针指向文件头并将文件大小截为零。如果文件不存在则创建该文件。 |
| a | 以写入方式打开，将文件指针指向文件末尾。如果文件不存在则创建该文件。 |
| a+ | 以读写方式打开，将文件指针指向文件末尾。如果文件不存在则创建该文件。 |
| x | 创建并以写入方式打开，将文件指针指向文件头。如果文件已存在，则 fopen() 调用失败并返回 FALSE，并生成一条 E_WARNING 级别的错误信息。如果文件不存在则创建该文件。仅适用于本地文件。 |
| x+ | 创建并以读写方式打开，其他的行为和 x 一样。 |
| c | 只打开文件进行写入，如果文件不存在，则创建该文件。如果文件存在，不会清空文件内容，并将文件指针指向文件头。 |
| c+ | 打开文件进行读写，如果文件不存在，则创建该文件。如果文件存在，不会清空文件内容，并将文件指针指向文件头。 |

*   $use_include_path：可选参数，如果也需要在 include_path 中搜寻文件的话，可以将 $use_include_path 设为 1 或 TRUE；
*   $context：可选参数，在 PHP5.0.0 中增加了对上下文（Context）的支持。

【示例】使用 fopen() 函数打开文件。

```

<?php
    $handle = fopen("./error/400.html", "r");
    var_dump($handle);echo '<br>';
    $handle = fopen("D:/install/phpstudy/WWW/index.html", "wb");
    var_dump($handle);echo '<br>';
    $handle = fopen("http://c.biancheng.net/", "r");
    var_dump($handle);
?>
```

运行结果如下：

resource(3) of type (stream)
resource(4) of type (stream)
resource(5) of type (stream)

## 3、关闭文件

资源类型属于 PHP 的基本类型之一，一旦完成资源的处理，一定要将其关闭，否则可能会出现一些意料不到的错误。

函数 fclose() 可以关闭一个已打开的文件指针，成功时返回 TRUE，失败则返回 FALSE。函数的语法格式如下：

fclose(resource $handle)

其中 $handle 为要关闭的文件指针，这个指针必须是有效，并且是通过 fopen() 或 fsockopen() 函数成功打开的。

【示例】使用 fclose() 关闭文件指针。

```

<?php
    $handle = fopen("http://c.biancheng.net/", "r");
    echo '文件指针关闭之前：';
    var_dump($handle);
    fclose($handle);
    echo '<br>文件指针关闭之后：';
    var_dump($handle);
?>
```

运行结果如下：

文件指针关闭之前：resource(3) of type (stream)
文件指针关闭之后：resource(3) of type (Unknown)