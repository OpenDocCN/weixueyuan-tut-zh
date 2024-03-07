# C#文件操作

> 原文：[`c.biancheng.net/csharp/100/`](http://c.biancheng.net/csharp/100/)

在前面操作变量和常量时这些值都是存放到内存中的，当程序运行结束后使用的数据全部被删除。

若需要长久保存应用程序中的数据，可以选用文件或数据库来存储。

文件通常存放到计算机磁盘上的指定位置，可以是记事本、Word 文档、图片等形式。

在 C# 语言中提供了相应的类用于直接在程序中实现对文件的创建、移动、读写等操作。

文件操作类在 System.IO 命名空间中，包括 Driveinfo 类、Directory 类、Directoryinfo 类、File 类、Filelnfo 类、Path 类等。