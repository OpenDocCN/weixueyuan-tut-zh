# C#异常与调试

> 原文：[`c.biancheng.net/csharp/130/`](http://c.biancheng.net/csharp/130/)

在编程中不可避免地会出现一些错误，错误主要包括编译错误和逻辑错误。

编译错误是很容易发现的，在 Visual Studio 2015 的代码页面中若出现一些红色的波浪线，表示程序无法运行；而逻辑错误是很难发现的，通常需要借助调试工具来查找。

此外，程序在运行过程中也会出现一些不可预料的问题，例如将字符串类型转换成整数时出现的异常、除数为 0 等。

在 C# 语言中，异常也称为运行时异常，它是在程序运行过程中出现的错误。

对于异常的处理需要程序员积累经验，在可能出现异常的位置加入异常处理语句。