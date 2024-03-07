# PHP imagecopy()：为图片添加水印

> 原文：[`c.biancheng.net/view/8035.html`](http://c.biancheng.net/view/8035.html)

为图片添加水印是图像处理中常见的操作。因为只要能在页面中见到的图片都是可以很轻松拿到的，如果你不希望辛辛苦苦编辑的图片被别人不费吹灰之力的拿走就用，就需要为图片添加水印以确定版权，防止图片被盗用。通常情况下，想要为图片添加水印，需要借助例如 Photoshop 等专业软件来实现。而 PHP 中借助 GD 扩展库中提供的函数也可以轻松实现为图片添加水印的功能。

制作水印一般可以使用文字（例如公司名称加网址），也可以使用图片（例如公司 LOGO），使用图片水印效果会更好一些，因为可以通过一些做图软件进行美化。

使用文字做水印，只需要在图片上画上一些文字即可。如果制作图片水印，就需要先了解一下 PHP 中的 imagecopy() 函数，该函数能复制图像的一部分，语法格式如下：

imagecopy(resource $dst_im, resource $src_im, int $dst_x, int $dst_y, int $src_x, int $src_y, int $src_w, int $src_h)

该函数可以将 $src_im 图像中坐标（$src_x，$src_y）的位置，拷贝一份宽度为 $src_w，高度为 $src_h 的矩形区域到 $dst_im 图像中坐标为 （$dst_x，$dst_y） 的位置上。

要使用图片水印的话，我们就需要明确水印图片的宽度和高度，除了可以使用 getimagesize() 函数外，还可以使用 PHP 中的 imagesx()、imagesy() 两个函数来分别获取图片的宽度和高度。

imagesx(resource $image)
imagesy(resource $image)

【示例】为图片添加水印。

```

<?php
    /**
     * [watermark description]
     * @param  string  $img              [待加水印的图片地址]
     * @param  string  $watermark        [水印图片地址]
     * @param  integer $district         [水印的位置]
     * @param  integer $watermarkquality [图片水印的质量]
     * @return                           [添加水印的图片]
     */
    function watermark($img, $watermark, $district = 0,$watermarkquality = 95){
        $imginfo = @getimagesize($img);
        $watermarkinfo = @getimagesize($watermark);
        $img_w = $imginfo[0];
        $img_h = $imginfo[1];
        $watermark_w = $watermarkinfo[0];
        $watermark_h = $watermarkinfo[1];
        if($district == 0) $district = rand(1,9);
        if(!is_int($district) OR 1 > $district OR $district > 9) $district = 9;
        switch($district){
            case 1:
                $x = +5;
                $y = +5;
                break;
            case 2:
                $x = ($img_w - $watermark_w) / 2;
                $y = +5;
                break;
            case 3:
                $x = $img_w - $watermark_w - 5;
                $y = +5;
                break;
            case 4:
                $x = +5;
                $y = ($img_h - $watermark_h) / 2;
                break;
            case 5:
                $x = ($img_w - $watermark_w) / 2;
                $y = ($img_h - $watermark_h) / 2;
                break;
            case 6:
                $x = $img_w - $watermark_w;
                $y = ($img_h - $watermark_h) / 2;
                break;
            case 7:
                $x = +5;
                $y = $img_h - $watermark_h - 5;
                break;
            case 8:
                $x = ($img_w - $watermark_w) / 2;
                $y = $img_h - $watermark_h - 5;
                break;
            case 9:
                $x = $img_w - $watermark_w - 5;
                $y = $img_h - $watermark_h - 5;
                break;
        }
        switch ($imginfo[2]) {
            case 1:
                $im = @imagecreatefromgif($img);
                break;
            case 2:
                $im = @imagecreatefromjpeg($img);
                break;
            case 3:
                $im = @imagecreatefrompng($img);
                break;
        }
        switch ($watermarkinfo[2]) {
            case 1:
                $watermark_logo = @imagecreatefromgif($watermark);
                break;
            case 2:
                $watermark_logo = @imagecreatefromjpeg($watermark);
                break;
            case 3:
                $watermark_logo = @imagecreatefrompng($watermark);
                break;
        }
        if(!$im or !$watermark_logo) return false;
        $dim = @imagecreatetruecolor($img_w, $img_h);
        if(@imagecopy($dim, $im, 0, 0, 0, 0,$img_w,$img_h )){
            imagecopy($dim, $watermark_logo, $x, $y, 0, 0, $watermark_w, $watermark_h);
        }
        $file = dirname($img) . '/w' . basename($img);
        $result = imagejpeg ($dim,$file,$watermarkquality);
        imagedestroy($watermark_logo);
        imagedestroy($dim);
        imagedestroy($im);
        if($result){
            echo $img.' 水印添加成功';
            return;
        }
        else {
            return false;
        }
    }

    $file = './back.gif';   //待加水印的图片地址
    $water = './logo.png';  //水印图片的地址
    watermark($file, $water);
?>
```

运行上面的代码，就会为 back.gif 添加水印，并将添加水印后的图片 wback.gif 保存在 back.gif 所在的目录中，添加水印后的图片如下所示：

![为图片添加水印](img/f4c0e67418cac3c86f9409fd7b9d4f85.png)
图：为图片添加水印