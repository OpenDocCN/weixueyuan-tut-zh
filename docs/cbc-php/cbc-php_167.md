# PHP 获取文件属性

> 原文：[`c.biancheng.net/view/7880.html`](http://c.biancheng.net/view/7880.html)

当我们在程序中操作文件时，可能会使用到文件的一些常见属性，比如文件的大小、类型、修改时间、访问时间以及权限等等。PHP 中提供了非常全面的用来获取这些属性的内置函数，如下表所示。

| 函数名 | 作用 | 语法格式 | 返回值 |
| file_exists() | 检查文件或目录是否存在 | file_exists(string $filename) | 文件存在返回 TRUE，不存在则返回 FALSE |
| filesize() | 获取文件大小 | filesize(string $filename) | 返回文件大小的字节数，出错时返回 FALSE |
| is_readable() | 判断给定文件名是否可读  | is_readable(string $filename) | 如果文件存在且可读则返回 TRUE，否则返回 FALSE |
| is_writable() | 判断给定文件名是否可写 | is_writable(string $filename) | 如果文件存在且可读写则返回 TRUE，否则返回 FALSE |
| is_executable() | 判断给定文件名是否可执行 | is_executable(string $filename) | 如果文件存在且可执行则返回 TRUE，否则返回 FALSE |
| filectime() | 获取文件的创建时间 | filectime(string $filename) | 返回 UNIX 时间戳 |
| filemtime() | 获取文件的修改时间 | filemtime(string $filename) | 返回 UNIX 时间戳 |
| fileatime() | 获取文件的上次访问时间 | fileatime(string $filename) | 返回 UNIX 时间戳 |
| stat() | 获取文件大部分属性值 | stat(string $filename) | 返回包含文件信息的数组 |

提示：在上表中的函数都需要提供一个同样的参数，即文件或目录的路径。

【示例】使用上表中的函数获取文件的属性。

```

<?php
    $file = 'test.txt';
    file_exists($file) or die('文件不存在，程序退出！');

    echo $file.' 文件大小是：'.filesize($file).' 个字节<br>';
    if(is_readable($file)){
        echo $file.' 文件是可读的。<br>';
    }else{
        echo $file.' 文件是不可读的。<br>';
    }
    if(is_writable($file)){
        echo $file.' 文件是可写的。<br>';
    }else{
        echo $file.' 文件是不可写的。<br>';
    }
    if(is_executable($file)){
        echo $file.' 文件是可执行的。<br>';
    }else{
        echo $file.' 文件是不可执行的。<br>';
    }
    echo '文件的创建时间是：'.date('Y-m-d H:i:s',filectime($file)).'。<br>';
    echo '文件的修改时间是：'.date('Y-m-d H:i:s',filemtime($file)).'。<br>';
    echo '文件上次的访问时间是：'.date('Y-m-d H:i:s',fileatime($file)).'。<br>';
?>
```

运行结果如下：

test.txt 文件大小是：387 个字节
test.txt 文件是可读的。
test.txt 文件是可写的。
test.txt 文件是不可执行的。
文件的创建时间是：2020-02-11 16:35:33。
文件的修改时间是：2020-06-11 11:30:44。
文件上次的访问时间是：2020-06-11 11:30:44。

除了使用这些函数分别获得文件的各种属性之外，还可以使用 stat() 函数来获取文件的大部分属性。stat() 函数接收一个文件名或者文件路径作为参数，并返回一个包含文件信息的数组，数组中包含的信息如下表所示。

| 数字下标 | 关联键名 | 说明 |
| 0 | dev | device number - 设备名 |
| 1 | ino | inode number - inode 号码 |
| 2 | mode | inode protection mode - inode 保护模式 |
| 3 | nlink | number of links - 被连接数目 |
| 4 | uid | userid of owner - 所有者的用户 id |
| 5 | gid | groupid of owner- 所有者的组 id |
| 6 | rdev | device type, if inode device * - 设备类型，如果是 inode 设备的话 |
| 7 | size | size in bytes - 文件大小的字节数 |
| 8 | atime | time of last access (unix timestamp) - 上次访问时间（Unix 时间戳） |
| 9 | mtime | time of last modification (unix timestamp) - 上次修改时间（Unix 时间戳） |
| 10 | ctime | time of last change (unix timestamp) - 上次改变时间（Unix 时间戳） |
| 11 | blksize | blocksize of filesystem IO * - 文件系统 IO 的块大小 |
| 12 | blocks | number of blocks allocated - 所占据块的数目 |

【示例】使用 stat() 函数来获取文件的信息。

```

<?php
    $file = 'test.txt';
    $arr = stat($file);
    echo "<pre>";
    print_r($arr);
?>
```

运行结果如下：

Array
(
    [0] => 19
    [1] => 0
    [2] => 33206
    [3] => 1
    [4] => 0
    [5] => 0
    [6] => 19
    [7] => 387
    [8] => 1591846244
    [9] => 1591846244
    [10] => 1581410133
    [11] => -1
    [12] => -1
    [dev] => 19
    [ino] => 0
    [mode] => 33206
    [nlink] => 1
    [uid] => 0
    [gid] => 0
    [rdev] => 19
    [size] => 387
    [atime] => 1591846244
    [mtime] => 1591846244
    [ctime] => 1581410133
    [blksize] => -1
    [blocks] => -1
)