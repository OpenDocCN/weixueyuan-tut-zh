# ARM 处理器的分类

> 原文：[`www.weixueyuan.net/a/134.html`](http://www.weixueyuan.net/a/134.html)

ARM 处理器的分类是一个比较复杂的过程，需要区分不同的架构、类型（系列）和具体的芯片型号，其中架构和类型（系列）是 ARM 公司提供的，而芯片型号则是由生产厂商所提供的。

ARM 公司提供了 ARMv1、ARMv2、ARMv3、ARMv4、ARMv5、ARMv6、ARMv7 和 ARMv8 共八种不同的架构，其中 ARMv1 和 ARMv2 都没有太大的实际使用价值，从 ARMv3 开始才逐步开始正式商用。

从 ARMv3 架构开始，ARM 推出了对应的 ARM6、7 处理器类型（系列），目前常见的 ARM 处理器类型（系列）有 ARM7、ARM9、ARM10、ARM11 和 Cortex。而每个系列处理器中又有许多不同的类型，如 ARM9 系列就有 ARM9E-S、ARM966E-S 等类型。

表 1 给出了 ARM 体系架构和具体的产品类型（系列）对应关系列表，其中后缀 -E 表明支持增强型 DSP 指令集、-J 表明支持新的 Java。

表 1：ARM 体系架构和对应的类型（系列）

| 体系架构 | 具体处理器类型（系列） |
| ARMv1 | ARM1 |
| ARMv2 | ARM2、ARM3 |
| ARMv3 | ARM6、ARM7 |
| ARMv4 | StrongARM、ARM7TDMI、ARM9TDMI、ARM940T、ARM920T、ARM720T |
| ARMv5 | ARM9E-S、ARM966E-S、ARM1020E、ARM 1022E、XScale、ARM9EJ-S、ARM926EJ-S、ARM7EJ-S、ARM1026EJ-S、ARM10 |
| ARMv6 | ARM11 系列（ARM1136J(F)-S、ARM1156T2(F)-S、ARM1176JZ(F)-S 和 ARM11 MPCore）、ARM Cortex-M |
| ARMv7 | ARM Cortex-A、ARM Cortex-M、ARM Cortex-R |
| ARMv8 | Cortex-A50 |

具体的产品型号则是得到授权的生产厂商根据 ARM 公司提供的具体处理器类型生产的芯片，如高通公司（Qualcomm）的处理器 MSM7201A 即是 ARM11 系列处理器，其对应的具体 ARM 类型是`ARM1136J(F)-S`，使用的是 ARMv6 架构，这块处理器应用在 HTC 公司的 Dream、Magic 等手机上。

对于 ARM 的处理器而言，其目前有 Classic（传统）系列、Cortex-M 系列、Cortex-R 系列、Cortex-A 系列和 Cortex-A50 系列 5 个大类；目前在消费数码产品中应用得最为广泛的是 Cortex-A 系列，它采用了 ARMv7 架构 ，包括了 Cortex-A8 和 Cortex-A9 等子系列。

不同系列 ARM 处理器的应用场景如下：

*   通常来说，作为工业控制处理器，可以选择 Cortex-M 系列处理器，其中 M0 比较简单便宜，适合用于替代 51 单片机，其缺点是不带 MMU，需要直接编写/运行控制代码；
*   Cortex-R 系列处理器可以取代 ARM9 作为具有带操作系统的控制系统；
*   Cortex-A 系列处理器更加常用的场合是消费电子，但是其中的 Cortex-A5 处理器也经常用于工业控制场合。