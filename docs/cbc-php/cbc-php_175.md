# PHP 图像处理

> 原文：[`c.biancheng.net/php/140/`](http://c.biancheng.net/php/140/)

PHP 除了可以处理文本数据外，通过一个叫 GD 的 PHP 扩展库，PHP 还可以动态生成不同格式图像或者对已有图片进行加工处理。

GD 库是一个开源的用于创建图形图像的函数库，该函数库由 C 语言编写，可以在 Perl、PHP 等多种语言中调试运用。PHP 使用 GD 库可以制作出各类丰富的图形图像效果，如统计图，为图片添加水印以及生成动态图表等。

另外，很多开源项目都对 GD 库提供了很好的技术支持，如 JpGraph 类库就是基于 GD 库开发的用于制作复杂统计图的类库，是对数据进行图形化分析的最佳方案之一，可以绘制各种统计图。本章将分别对 GD 库以及 JpGraph 类库进行详细讲解。