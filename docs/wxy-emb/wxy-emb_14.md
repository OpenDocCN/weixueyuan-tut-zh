# ARM Cortex-A 系列处理器

> 原文：[`www.weixueyuan.net/a/135.html`](http://www.weixueyuan.net/a/135.html)

Cortex-A 处理器包括 Cortex-A5、Cortex-A7、Cortex-A8、Cortex-A9、Cortex-A12 和 Cortex-A15 共 6 个子系列，用于具有高计算要求、运行丰富操作系统及提供交互媒体和图形体验的应用领域，如智能手机、平板电脑、汽车娱乐系统、数字电视等。

Cortex-A 系列处理器具有以下特点：

*   是移动互联网的理想选择，其为 Adobe Flash10.1 以上版本提供了原生支持；提供了高性能 NEON 引擎，广泛支持媒体解码器；采用了低功耗设计，支持全天浏览和连接；
*   其具有高性能，可以为其目标应用领域提供各种可伸缩的能效性能点；
*   都支持 ARM 的第二代多核技术；支持面向性能的应用领域的单核到四核的实现；支持对称和非对称的操作系统实现；并且可以通过加速器一致性端口（ACP）在系统的整个处理器中保持一致性。并且 Cortex-A5 和 Cortex-A15 将多核一致性扩展至 AMBA ACE 的 1～4 核群集以上，支持 big LITTLE（ARM big.LITTLETM 处理是一项节能技术，它将最高性能的 ARM 处理器与最高效的 ARM 处理器结合到一个处理器子系统中，与当今业内最优秀的系统相比，不仅性能更高，能耗也更低）处理。
*   除了具有与上一代经典 ARM 和 Thumb 架构的二进制兼容性之外，Cortex-A 类处理器还可以通过 Thumb-2、TrustZone 安全扩展、Jazelle 技术扩展来提供更多的优势。

Cortex-A 系列处理器均适用于各种不同的性能应用领域，其共享 ARMv7-A 的架构和功能集，成为开放式平台设计的最佳解决方案并且可以为不同设计之间的软件提供兼容性和可移植性，其提供了 ARM、Thumb-2、Thumb、Jazelle、DSP 的指令集支持、TrustZone 安全扩展、高级单精度和双精度浮点支持、NEON 媒体处理引擎及对包括 Linux 全部分发版本（Android、Chrome、Ubuntu 和 Debian）、Linux 第三方（MontaVista、QNX、Wind River、Symbian、Windows CE）、需要使用内存管理单元的其他操作系统支持。

Cortex-A 也提供了各种显著不同的特点，如表 4 所示。

表 4：Cortex-A 系列处理器比较

| 内核 | Cortex-A5 | Cortex-A5 多核 | Cortex-A8 | Cortex-A9 | Cortex-A9 多核 | Cortex-A9 硬核 | Cortex-A15 多核 | Cortex-A7 多核 |
| 架构 | ARMv7 | ARMv7+MP | ARMv7 | ARMv7 | ARMv7+MP | ARMv7+MP | ARMv7+MP+LPAE | ARMv7+MP+LPAE |
| 中断控制器 | GIC-390 | 已集成-GIC | GIC-390 | GIC-390 | 已集成-GIC | 已集成-GIC | 已集成-GIC | GIC-400 |
| L2 cache 控制器 | L2C-310 | L2C-310 | 已集成 | L2C-310 | L2C-310 | L2C-310 | 已集成 | 已集成 |
| 预期实现 | 300~800MHz | 300~800MHz | 600~1000MHz | 600~1000MHz | 600~1000MHz | 800~2000MHz | 1000~2500MHz | 800~1500MHz |
| DMIPS/MHz | 1.6 | 1.6/ 处理器 | 2.0 | 2.5 | 2.5/ 处理器 | 5.0/双核 | TBC | 1.9/ 处理器 |

#### 1\. Cortex-A5

Cortex-A5 处理器是体积最小、功耗最低的应用型处理器，并且可以带来完整的网络体验，可为现有的 ARM926EJ-S 和 ARM1176JZ-S 处理器设计提供高价值的迁移途径。它可实现比 ARM1176JZ-S 更好的性能、比 ARM926EJ-S 更好的功效和能效，以及 100% 的 Cortex-A 兼容性。

#### 2\. Cortex-A7

Cortex-A7 处理器是一种高能效应用处理器，除了低功耗应用外，还支持低成本、全功能入门级智能手机，该处理器与其他 Cortex-A 系列处理器完全兼容并整合了高性能 Cortex-A15 处理器的所有功能，包括虚拟化、大物理地址扩展（LPAE）NEON 高级 SIMD 和 AMBA 4 ACE 一致性。单个 Cortex-A7 处理器的能效是 ARM Cortex-A8 处理器的 5 倍，性能提升了 50%，而尺寸仅为后者的五分之一，支持如今的许多主流智能手机。

目前提供 Cortex-A7 的厂商包括德州仪器（TI）、三星（SAMSUNG）、飞思卡尔（Freescale）、博通（Broadcom）、海思半导体（HISILICON）和 LG。

#### 3\. Cortex-A8

Cortex-A8 处理器基于 ARMv7 架构，支持 1GHz 以上的工作频率，采用了高性能、超标量微架构及用于多媒体和 SIMD 处理的 NEOD 技术，可以满足 300mW 以下运行的移动设备的低功耗要求，并且与 ARM926、ARM1136 和 ARM1176 处理器的二进制兼容。

目前提供 Cortex-A8 的厂商有德州仪器（TI）、三星（SAMSUNG）、飞思卡尔（Freescale）、博通（Broadcom）和 ST（意法半导体）。

#### 4\. Cortex-A9

Cortex-A9 处理器是低功耗或散热受限的成本敏感型设备的首选处理器，其支持多核，在用作单核心的时候性能比 Cortex-A8 提升了 50% 以上，其主要用于主流智能手机、平板电脑、多媒体播放器等。还可以提供多达 4 个处理器集成，在必要的时候能实现轻量级的工作量及峰值性能。

#### 5\. Cortex-A12

Cortex-A12 是 Cortex-A9 的升级版，专注应用于智能手机和平板电脑，提供了对 1TB 存储空间的支持，在同功耗下其比 Cortex-A9 性能提升了大约 40%，通常来说该芯片使用较少，故在此不多再赘叙。

#### 6\. Cortex-A15

Cortex-A15 处理器是 Cortex-A 系列处理器的最新产品，也是最高性能产品，和其他处理器系列兼容，具有无序超标量流水线，带有紧密耦合的大小可以达到 4MB 的低延迟 2 级 cache；改进后的浮点和 NEON 媒体性能可以给用户提供下一代的体验并为 Web 基础结构应用提供高性能计算。其通常应用于移动计算、高端数码家电、服务器和无线基础架构。

Cortex-A15 是基于 ARMv7 架构的处理器，其和 Cortex-A 系列的其他处理器完全兼容，所以支持相当多成熟的开发平台和软件体系，包括 Android、AFP（Adobe Flash Player）、Java Platform Standard Edition（Java SE）、JavaFX、Linux、Microsoft Windows Embedded、Symbian 和 Ubuntu 等。

Cortex-A15 还支持 big.LITTLE 技术，其可以和 Cortex-A7 处理器配对并且确保合适的工作分配给合适的处理器，以达到合适的性能和功耗的平衡。

#### 7\. Cortex-A50 系列

Cortex-A50 系列处理器基于 ARMv8 架构，可以在 AArch32 执行状态下为 ARMv7 的 32 位代码提供更好的性能，也可以在 AArch64 执行状态下支持 64 位数据和更大的虚拟寻址空间，其允许在 32 位和 64 位之间进行完全的交互操作，因此可以从运行 32 位 ARMv7 应用程序的 64 位操作系统开始，迁移到在同一系统中混合运行 32 位应用程序和 64 位应用程序，最终一步步迁移到 64 位系统。

Cortex-A50 提供了 A53 和 A57 两种型号的处理器，由于该系列处理器刚刚发展起来，还没有大规模实际应用，在此不多做赘述。