# PHP fwrite()、file_put_contents()：向文件中写入数据

> 原文：[`c.biancheng.net/view/7872.html`](http://c.biancheng.net/view/7872.html)

前面我们介绍了打开和读取文件，本节我们就来介绍一下文件的写入操作。在 PHP 中将程序中的数据保存到文件中相对比较容易，使用 fwrite() 和 file_put_contents() 函数就可以将字符串内容写入文件中，下面就来分别介绍一下。

## 1、fwrite() 函数

fwrite() 函数可以将一个字符串写入到文件中，函数的语法格式如下：

fwrite(resource $handle, string $string[, int $length])

参数说明如下：

*   $handle：待写入的文件，是由 fopen() 创建的 resource（资源）；
*   $string：要写入的字符串；
*   $length：可选参数，用来设定要写入的字节数。

fwrite() 函数可以把 $string 的内容写入文件指针 $handle 处。 如果指定了 $length，当写入了 $length 个字节或者写完了 $string 以后，写入就会停止。函数执行成功，会返回写入的字节数，执行失败，则返回 FALSE。

【示例】使用 fwrite() 函数向文件中写入指定的字符串。

```

<?php
  $fp = fopen('test.txt', 'w');
  fwrite($fp, 'http://c.biancheng.net/');
  fwrite($fp, 'php/');
  fclose($fp);
  echo "<pre>";
  print_r(file('test.txt'));
?>
```

运行结果如下：

Array
(
    [0] => http://c.biancheng.net/php/
)

注意：使用 fwrite() 函数向文件中写入的内容会覆盖文件中原有的内容。

## 2、file_put_contents() 函数

file_put_contents() 函数与 fwrite() 函数功能相同，同样可以将一个字符串写入到文件中，语法格式如下：

file_put_contents(string $filename, mixed $data[, int $flags = 0[, resource $context]])

参数说明如下：

*   $filename：要被写入数据的文件名；
*   $data：要写入的数据，可以是字符串、一维数组或者资源等类型；
*   $flags：可选参数，它的值可以是以下几种（可以使用 `|` 运算符组合使用）：
    *   FILE_USE_INCLUDE_PATH：在 include 目录里搜索 $filename。
    *   FILE_APPEND：如果文件 $filename 已经存在，追加数据而不是覆盖。
    *   LOCK_EX：在写入时获得一个独占锁。
*   $context：可选参数，一个 context 资源。

与 fwrite() 函数相同 file_put_contents() 函数执行成功会返回写入到文件内数据的字节数，失败时返回布尔值 FALSE 或者等同于 FALSE 的非布尔值。

【示例】使用 file_put_contents() 函数向文件中写入指定的数据。

```

<?php
  $file = 'test.txt';
  echo "<pre>写入前：";
  echo file_get_contents($file).'<br>';
  $arr = [
    'C 语言中文网',
    'http://c.biancheng.net/'
  ];
  file_put_contents($file, $arr,FILE_APPEND|LOCK_EX);
  echo '写入后：'.file_get_contents($file);
?>
```

运行结果如下：

写入前：test
写入后：testC 语言中文网 http://c.biancheng.net/

在文件中通过字符序列 `\n` 表示换行符，代表文件中一行的结尾。不同的操作系统具有不同的结束符号，基于 UNIX 的系统使用 `\n` 作为行结束字符，基于 Windows 的系统使用 `\r\n` 作为行结束字符，基于 Macintosh 的系统使用 `\r` 作为行结束字符。当要写入一个文本文件并想插入一个新行时，需要使用相应操作系统的行结束符号。