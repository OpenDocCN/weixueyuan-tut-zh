# PHP JpGraph 简介及安装方法

> 原文：[`c.biancheng.net/view/8048.html`](http://c.biancheng.net/view/8048.html)

应用 GD 函数库可以创建各式各样的图形，但是想要制作复杂的统计图形，仅通过 GD 函数来实则会显得非常繁琐，PHP 从诞生到走向成熟的过程中经过多次变革，在这期间，许多组织和机构都开发了基于 PHP 的框架和类库。JpGraph 图形类库就是其中非常实用、流行的代表，不仅功能强大，而且操作简便。

## JpGraph 类简介

JpGraph 是一个完全使用 PHP 编写的，基于 GD2 函数库的用于创建统计图的类库。JpGraph 在绘制统计图方面不仅功能非常强大，而且使用方便，只需简单的几行代码就可以绘制出非常复杂的统计图效果，从而在很大程度上提高了编程人员的开发效率。

使用 JpGraph 可以根据需要绘制任意图形。我们只需要提供相关数据、标题以及表格类型，然后把剩下的事交给 JpGraph 就行。只需掌握为数不多的 JpGraph 内置函数，就可以制作出非常漂亮的图表。

## JpGraph 类安装

想要使用 JpGraph，首先我们需要从官方网站 [`jpgraph.net/download/`](https://jpgraph.net/download/) 下载 JpGraph。这里我们下载的是 JpGraph 4.3.1 版本，需要注意的是 JpGraph 4.x 系列支持 PHP5（PHP 5.1.0 或更高版本）和 PHP 7（PHP 7.0、7.1 和 7.2）。在下载 JpGraph 时注意选择合适的版本进行下载。

JpGraph 需要 GD 库的支持，如果我们想在当前站点中使用 JpGraph 类库的话，只需要将下载下来的 JpGraph 压缩包中的 src 目录复制到当前站点所在目录中，使用时只需要调用 src 目录下的指定文件即可。

如果想要在服务器上的所有站点中使用 JpGraph，可以执行如下步骤来安装 JpGraph 类库：

*   将压缩包下的全部文件解压到一个文件夹中，例如“D:\www”；
*   打开 PHP 的安装目录，找到并打开 php.ini 文件，然后修改其中的 include_path 参数，在其后增加上一步解压出来的文件夹名，例如“include_path = ".;D:\www"”；
*   重新启动 Apache 服务器即可生效。

## JpGraph 配置

JpGraph 提供了几个专门用于配置 Jperaph 类库的文件，例如 jpg-config.inc.php、jpgraph_ttf.inc.php 等等，在使用 JpGraph 前，可以通过修改文本文件来完成 JpGraph 的配置。

#### 1) 中文字体配置

JpGraph 支持的中文标准字体可以通过修改 CHINESE_TTF_FONT 的设置来完成，可以在配置文件 jpgraph_ttf.inc.php 中找到该配置项。

// Actual name of the TTF file used together with FF_CHINESE aka FF_BIG5
// This is the TTF file being used when the font family is specified as
// either FF_CHINESE or FF_BIG5
define('CHINESE_TTF_FONT','bkai00mp.ttf');

#### 2) 默认图片格式配置

根据当前 PHP 环境中支持的图片格式来设置默认的生成图片的格式。JpGraph 默认图片格式的配置可以通过修改配置文件 jpg-config.inc.php 中的 DEFAULT_GFORMAT 项来完成。默认值 auto 表示 JpGraph 将依次按照 PNG、GIF 和 JPEG 的顺序来检索系统支持的图片格式。

// Deafult graphic format set to 'auto' which will automatically
// choose the best available format in the order png,gif,jpeg
// (The supported format depends on what your PHP installation supports)
define('DEFAULT_GFORMAT','auto');