# PHP imagedestroy():释放图像资源

> 原文：[`c.biancheng.net/view/7975.html`](http://c.biancheng.net/view/7975.html)

在图像的所有资源使用完毕后，通常需要释放图像处理所占用的内存。在 PHP 中可以通过 imagedestroy() 函数来释放图像资源，其语法格式如下所示：

imagedestroy(resource $image)

其中，$image 为要释放的图像资源。

【示例】在图像处理程序执行完毕后使用 imagedestroy() 函数来释放图像资源。

```

<?php
    header('Content-type:image/png');
    $image = imagecreatefrompng('http://c.biancheng.net/templets/new/images/logo.png');
    imagepng($image);
    imagedestroy($image);
?>
```

运行上面的代码会输出 C 语言中文网的 logo。另外，释放图像资源的操作是没有输出内容的，那么要怎么证明图像是不是被释放了呢？其实我们可以在输出图像之前就执行释放图像资源，示例代码如下所示：

```

<?php
    header('Content-type:image/png');
    $image = imagecreatefrompng('http://c.biancheng.net/templets/new/images/logo.png');
    imagedestroy($image);
    imagepng($image);
?>
```

运行结果如下：

Warning: imagepng(): supplied resource is not a valid Image resource in D:\WWW\index.php on line 5

提示：使用 Chrome 浏览器可能无法输出上面的错误信息，而是输出一个空白的图像。上面的错误提示是通过 IE 浏览器输出的。

通过上面的示例代码可以看出，使用 imagedestroy() 函数确实可以释放图像资源。