# Android 电话短信拍照开发教程

> 原文：[`c.biancheng.net/android/50/`](http://c.biancheng.net/android/50/)

Intent 是组成 Android 应用程序的主要组成部分之一。在 Intent 对象中指定程序将要执行的动作（Action），以及程序执行该动作时所需要的数据（Data），并使用 startActivity()方法启动该 Intent 对象。

本章简单介绍了显式 Intent 和隐式 Intent 的使用方法，并举例说明了使用 Intent 调用手机短信和拨号程序的方法，以及调用系统摄像头应用程序的方法。

借助于 Intent，程序员可以方便地调用 Android 系统中的其他应用程序，例如启动网络浏览器进行网上冲浪、收发 E-Mail 等。读者可举一反三，通过 Intent 使自己开发的应用程序更加方便和强大。