# 第十四章 Python 并发编程

![](img/0f7a3239373c99cbd71e7d11968385b6.jpg)相比单线程的编程，多线程的程序可包含多个顺序执行流，这些顺序执行流之间互不干扰。也可以这样理解，单线程的程序如同只雇佣一个服务员的餐厅，他必须做完一件事情后才可以做下一件事情；而多线程的程序则如同雇佣多个服务员的餐厅，他们可以同时做多件事情。

Python 语言提供了非常优秀的多线程支持，程序可以通过非常简单的方式来启动多线程。

本章将会详细介绍 Python 多线程编程的相关知识，包括创建、启动线程，控制线程，以及多线程的同步操作，并会介绍如何利用 Python 内建支持的线程池来提高多线程的性能。