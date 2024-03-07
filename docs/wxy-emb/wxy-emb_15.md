# 嵌入式存储器概述

> 原文：[`www.weixueyuan.net/a/338.html`](http://www.weixueyuan.net/a/338.html)

存储器是用于存放系统软件和用户软件的载体：前者包括了启动引导软件、操作系统和硬件驱动；后者是用户用于实现应用目的而设计的软件。

在嵌入式系统设计中需要考虑如何安排这些存储器的地址，尤其是 NOR Flash 和 SDRAM，因为它们涉及了系统的启动步骤等。

嵌入式硬件中常用的存储器件包括 SDRAM、FLASH、E2PROM、大容量存储系统（SD 卡、U 盘、硬盘）等，接下来我们将使用两篇文章来详细介绍 SDRAM 和 FLASH 两种存储器：

*   SDRAM 存储器
*   FLASH 存储器

E2PROM 和大容量存储系统简单了解一下就行，就不单独介绍了。

#### E2PROM

E2PROM（Electrically Erasable Programmable Read-Only Memory）主要用于在嵌入式系统中保存一些小量数据或特殊用途数据，通常使用串行通信接口和嵌入式处理器进行数据交互，如 I2C 总线接口、SPI 总线接口和 1-wire 总线接口等，其容量通常在几百字节到几百万字节不等。

#### 大容量存储器

嵌入式系统中的大容量存储系统包括 SD 卡、U 盘和普通硬盘，通常用于保存大容量数据，和嵌入式处理器通过对应的接口芯片或时序进行数据交互。