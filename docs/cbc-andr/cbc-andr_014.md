# Android 资源（res 和 assets 目录）

> 原文：[`c.biancheng.net/view/2939.html`](http://c.biancheng.net/view/2939.html)

在 Android 层次结构中，资源扮演着重要的角色。

Android 支持字符串、位图以及其他多种类型的资源。每一种资源的语法、格式以及存放的位置都会根据其类型的不同而不同。

一般来讲，共有三种类型的资源文件：XML 文件、位图文件（图像）和 RAW 文件（声音等）。

Android 工程目录中，用于存放资源文件的文件夹有两个，分别为 res 和 assets。

res 和 assets 的区别

| res | assets |
| 不支持深度子目录 | 可以使用任意深度的子目录进行存储 |
| 资源最终将被打包到编译后的 Java 文件中，可以直接通过 R 资源类访问 | 资源打包到应用程序中的静态文件，这些文件不会被编译，最终会直接部署到目标设备中；另外，不能直接通过 R 资源类读取，只能使用流的形式读取。 |
| 利用率较高 | 利用率相对较低 |

Android 的资源编译器 AAPT（Android Asset Packaging Tool）会依照资源所在的子目录及其格式对其进行编译。