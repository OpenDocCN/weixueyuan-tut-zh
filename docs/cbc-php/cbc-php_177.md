# PHP imagecreatetruecolor()和 imagecreate()：创建画布

> 原文：[`c.biancheng.net/view/7940.html`](http://c.biancheng.net/view/7940.html)

在 PHP 中，通过 GD 库处理图像的操作，都是先在内存中处理，操作完成以后再以文件流的方式，输出到浏览器或保存在服务器的磁盘中。创建图像一般分为 4 个基本步骤。

*   创建画布：所有的绘图设计都需要在一个背景图片上完成，而画布实际上就是在内存中开辟的一块临时区域，用于存储图像的信息。以后的图像操作都将基于这个背景画布，该画布的管理就类似于我们在画画时使用的画布。
*   绘制图像：画布创建完成以后，就可以通过这个画布资源，使用各种画像函数设置图像的颜色、填充画布、画点、线段、各种几何图形，以及向图像中添加文本等。
*   输出图像：完成整个图像的绘制以后，需要将图像以某种格式保存到服务器指定的文件中，或将图像直接输出到浏览器上显示给用户。在图像输出之前，一定要使用 header() 函数发送 Content-type 通知浏览器，这次发送的是图片不是文本。
*   释放资源：图像被输出以后，画布中的内容也不再有用。出于节约系统资源的考虑，需要及时清除画布占用的所有内存资源。

本节我们主要来介绍一下如何创建一个画布，其它的步骤会在后面的小节中介绍。

在使用 GD 库处理图像时，首先要创建一张画布。创建画布就是在内存中开辟一块存储区域，以后 GD 库的所有操作都是基于这个画布处理的。

通常使用 imagecreate() 和 imagecreatetruecolor() 来创建指定的画布，它们的语法格式如下所示：

imagecreate(int $width, int $height)
imagecreatetruecolor(int $width, int $height)

其中，$width 为图像的宽度，$height 为图像的高度。

以上两个函数都可以创建一张画布，而且成功后都会返回一个资源句柄，失败则返回 FALSE。不同的是它们可以容纳的色彩范围不同，imagecreate() 创建一个基于普通调色板的图像，通常支持 256 色；而 imagecreatetruecolor() 可以创建一个真色彩图像，但是它不能用于 GIF 格式图像。

【示例】使用 imagecreate() 和 imagecreatetruecolor() 函数创建指定尺寸的画布。

```

<?php
    header ('Content-Type: image/png');
    $im  = @imagecreate(100, 50) or die("画布 1 创建失败！");
    $img = @imagecreatetruecolor(120, 20) or die('画布 2 创建失败！');
?>
```

由于没有在画布上执行任何操作，所以浏览器不会输出画布。但是可以通过 imagesx() 和 imagesy() 来获取图像的宽和高（单位是像素），它们的语法格式如下所示：

imagesx(resource $image)
imagesy(resource $image)

其中 $image 为创建的画布资源。

【示例 】使用 imagesx() 和 imagesy() 函数获取新建图像的宽和高。

```

<?php
    $img = @imagecreatetruecolor(120, 20) or die('画布创建失败！');
    echo '画布的宽度为：'.imagesx($img).'像素';
    echo '<br>画布的高度为：'.imagesy($img).'像素';
?>
```

运行结果如下：

画布的宽度为：120 像素
画布的高度为：20 像素

除了使用上面两个函数可以创建画布外，还可以使用下表中的函数，通过文件或 URL 创建一个新图像。

| 函数名 | 描述 |
| imagecreatefromgif() | 通过 GIF 文件或者 URL 新建一个图像 |
| imagecreatefromjpeg()  | 通过 JPEG 文件或者 UR 新建一个图像 |
| imagecreatefrompng()   | 通过 PNG 文件或者 UR L 新建一个图像 |
| imagecreatefromwbmp() | 通过 WBMP 文件或者 URL，新建一个图像 |

上表中的函数都只接受一个文件路径或者 URL 作为参数，在执行成功后返回文件句柄，失败则返回 FALSE。