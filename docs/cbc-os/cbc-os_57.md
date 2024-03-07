# I/O 设备及其分类

I/O 设备管理是操作系统设计中最凌乱也最具挑战性的部分。由于它包含了很多领域的不同设备以及与设备相关的应用程序，因此很难有一个通用且一致的设计方案。所以在理解设备管理之前，应该先了解具体的 I/O 设备类型。

计算机系统中的 I/O 设备按使用特性可分为以下类型：

1) 人机交互类外部设备：用于同计算机用户之间交互的设备，如打印机、显示器、鼠标、键盘等。这类设备数据交换速度相对较慢，通常是以字节为单位进行数据交换。

2) 存储设备：用于存储程序和数据的设备，如磁盘、磁带、光盘等。这类设备用于数据交换，速度较快，通常以多字节组成的块为单位进行数据交换。

3) 网络通信设备：用于与远程设备通信的设备，如各种网络接口、调制解调器等。其速度介于前两类设备之间。网络通信设备在使用和管理上与前两类设备也有很大不同。

除了上面最常见的分类方法，I/O 设备还可以按以下方法分类：

1) 按传输速率分类：

*   低速设备：传输速率仅为每秒几个到数百个字节的一类设备，如键盘、鼠标等。
*   中速设备：传输速率在每秒数千个字节至数万个字节的一类设备，如行式打印机、 激光打印机等。
*   高速设备：传输速率在数百个千字节至千兆字节的一类设备，如磁带机、磁盘机、 光盘机等。

2) 按信息交换的单位分类：

*   块设备：由于信息的存取总是以数据块为单位，所以存储信息的设备称为块设备。它属于有结构设备，如磁盘等。磁盘设备的基本特征是传输速率较高，以及可寻址，即对它可随机地读/写任一块。
*   字符设备：用于数据输入/输出的设备为字符设备，因为其传输的基本单位是字符。它属于无结构类型，如交互式终端机、打印机等。它们的基本特征是传输速率低、不可寻址，并且在输入/输出时常釆用中断驱动方式。