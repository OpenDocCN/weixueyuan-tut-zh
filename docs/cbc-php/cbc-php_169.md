# PHP 文件上传

> 原文：[`c.biancheng.net/view/7901.html`](http://c.biancheng.net/view/7901.html)

文件上传是 PHP 文件系统的重要功能之一，要使用文件上传功能，我们首先需要在配置文件 php.ini 中对上传做一些设置；然后通过预定义变量 $_FILES 对上传文件做一些限制和判断；最后使用 move_uploaded_file() 函数来实现上传。

本节的学习内容，会涉及一小部分的 HTML 知识。大家有 HTML 基础最好，没有也完全不要害怕，重点的知识在 PHP 而不是 HTML。

## 1、配置 php.ini

实现文件上传功能，首先要在 php.ini 中开启文件上传，并对其中的一些参数作出合理的设置。找到 Uploads 项，可以看到下面 3 个属性值，其含义如下：

*   file_uploads：如果值是 on，则说明服务器开启了文件上传功能：如果为 off，则说明服务器关闭了文件上传功能；
*   upload_tmp_dir：上传文件的临时目录。在文件被成功上传之前，文件首先会存放到服务器端的临时目录中，不设置的为系统默认的目录；
*   upload_max_filesize：服务器允许上传文件的最大值，以 MB 为单位，系统默认为 2MB，用可以自行设置。

除了 File Uploads 项，还有几个属性也会影响到上传文件的功能：

*   max_execution_time：PHP 中一个指令所能执行的最大时间，单位是秒；
*   memory_limit：PHP 中一个指令所分配的内存空间，单位是 MB。

一般情况下将 php.ini 配置称下面的样子即可，当然也可以根据自己的需求做相应的调整。

; Whether to allow HTTP file uploads.
; http://php.net/file-uploads
file_uploads = On

; Temporary directory for HTTP uploaded files (will use system default if not
; specified).
; http://php.net/upload-tmp-dir
;upload_tmp_dir =

; Maximum allowed size for uploaded files.
; http://php.net/upload-max-filesize
upload_max_filesize = 100M

; Maximum number of files that can be uploaded via a single request
max_file_uploads = 100

; Maximum amount of memory a script may consume (128MB)
; http://php.net/memory-limit
memory_limit=256M

; Maximum execution time of each script, in seconds
; http://php.net/max-execution-time
; Note: This directive is hardcoded to 0 for the CLI SAPI
max_execution_time = 300

提示：php.ini 文件配置完成后，需要重新启动 Apache 服务器，配置才能生效。

## 2、预定义变量 $_FILES

$_FILES 变量存储的是上传文件的相关信息，这些信息对于上传功能有很大的作用。该变量是一个二维数组。保存的信息如下表所示。

| 数组元素 | 保存的信息 |
| $_FILES[filename][name] | 保存上传文件的文件名 |
| $_FILES[filename][size] | 保存上传文件的大小 |
| $_FILES[filename][tmp_name] | 保存上传文件的临时名称 |
| $_FILES[filename][type] | 保存上传文件的类型 |
| $_FILES[filename][error] | 保存上传文件结果的代号，0 则表示成功 |

【示例】使用 $_FILES 与 HTML 表单相结合，获取上传文件的信息。

```

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>C 语言中文网——PHP 文件上传</title>
</head>
<body>
    <form action="" method="post" enctype="multipart/form-data">
        <input type="file" name="upfile">
        <input type="submit" value="上传">
    </form>
</body>
</html>
<?php
    if(!empty($_FILES)){
        foreach ($_FILES['upfile'] as $key => $value) {
            echo $key.'=>'.$value.'<br>';
        }
    }
?>
```

运行上面的程序，然后选择要上传的文件，并单击上传按钮就可以打印出文件的相关信息，如下所示：

name=>脚本.jsf
type=>application/octet-stream
tmp_name=>C:\Windows\php6E1A.tmp
error=>0
size=>25551

## 3、单文件上传

通过上面的代码我们已经可以获取到上传文件的信息了，通过这些信息再联合 move_uploaded_file() 函数就可以实现文件上传。move_uploaded_file() 函数的主要功能就是将上传的文件移动到新的位置，执行成功时返回 TRUE，否则返回 FALSE，函数的语法格式如下所示：

move_uploaded_file(string $filename, string $destination)

其中，$filename 为上传文件的文件名；$destination 为文件要移动到的位置。

注意：$filename 接受的是文件上传后的临时名称而不是文件的原名称，它可以通过 $_FILES 得到。

【示例】将文件上传到根目录的 uploads 文件夹中（注意：需要先在网站根目录下新建一个 uploads 文件夹）。

```

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>C 语言中文网——PHP 文件上传</title>
</head>
<body>
    <form action="" method="post" enctype="multipart/form-data">
        <input type="file" name="upfile">
        <input type="submit" value="上传">
    </form>
</body>
</html>
<?php
    if(!empty($_FILES)){
        $tmpname   = $_FILES['upfile']['tmp_name'];     // 临时文件名称
        $name      = $_FILES['upfile']['name'];         // 文件的原名称
        $path      = './uploads';                       // 上传目录
        $file_name = date('YmdHis').rand(100,999).$name;// 避免文件重名，更改文件名称
        if(move_uploaded_file($tmpname, $path.'/'.$file_name)){
            echo $name." 上传成功！";
        }else{
            echo $name." 上传失败！";
        }
    }
?>
```

运行上面的代码，选择文件并点击上传按钮后，选择的文件就会上传到 uploads 目录中。

## 4、多文件上传

前面我们成功上传了一个文件，但有些时候我们往往需要一次上传多个文件。例如上传相片到博客就需要多个文件同时上传。实现多文件上传也非常简单，下面我们就通过示例来演示以下。

【示例】同时上传多个文件到根目录的 uploads 文件夹中。

```

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>C 语言中文网——PHP 文件上传</title>
</head>
<body>
    <form action="" method="post" enctype="multipart/form-data">
        <input type="file" name="upfile[]"><br>
        <input type="file" name="upfile[]"><br>
        <input type="file" name="upfile[]"><br>
        <input type="submit" value="上传">
    </form>
</body>
</html>
<?php
    if(!empty($_FILES)){
        $tmpname = $_FILES['upfile']['tmp_name'];
        $name = $_FILES['upfile']['name'];
        $path = './uploads';
        for ($i=0; $i < count($tmpname); $i++) {
            $file_name = date('YmdHis').rand(100,999).$name[$i];
            if(move_uploaded_file($tmpname[$i], $path.'/'.$file_name)){
                echo $name[$i].' 上传成功！<br>';
            }else{
                echo $name[$i].' 上传失败！<br>';
            }
        }
    }
?>
```