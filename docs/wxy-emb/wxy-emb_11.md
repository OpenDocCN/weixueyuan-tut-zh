# ARM Classic（传统）系列处理器

> 原文：[`www.weixueyuan.net/a/335.html`](http://www.weixueyuan.net/a/335.html)

ARM Classic（传统）系列处理器上市已经超过 15 年，其中的 ARM7TDMI 依然是市场占有率最高的 32 位处理器，该系列处理器由三个子系列九种处理器组成，分别是：
● ARM7 系列：基于 ARMv3 或 ARMv4 架构，包括 ARM7TDMI-S 和 ARM7EJ-S 处理器。
● ARM9 系列：基于 ARMv5 架构，包括 ARM926EJ-S、ARM946E-S 和 ARM968E-S 处理器。
● ARM11 系列：基于 ARMv6 架构，包括 ARM1136J(F)-S、ARM1156T2(F)-S、ARM1176JZ(F)-S 和 ARM11MPCore 处理器。

Classic 系列处理器在很大程度上已经逐步被 Cortex 系列所取代，所以在最新的设计中并不推荐使用该系列处理器，尤其是 ARM7 系列。

ARM 的 Classic（传统）系列内核主要基于三种主要技术产品而构建，可以用于各种应用领域，表 1 给出了它们的技术说明。

表 1：ARM 的 Classic 系列内核

| 技术 | ARM7 ARMv4T 架构 | ARM9 ARMv5TE 架构 | ARM11 ARMv6 架构 |
| ARM ISA（指令集架构） | √ | √ | √ |
| Thumb ISA | √ | √ | √ |
| Thumb-2 ISA | × |   | √（仅 ARM1156T2-S） |
| DSP 扩展 | × | √ | √ |
| SIMD 扩展 | × | × | √ |
| Jazelle 字节码支持 | × | √（仅 ARM926EJ-S） | √（ARM1156T2-S 除外） |
| 浮点支持 | × | √（VFP9） | √（VFP11） |
| TrustZone 安全扩展 | × | × | √（仅 ARM1176JZ(F)-S） |
| cache 支持 | × | √ | √ |
| TCM（紧密耦合内存）支持 | × | √ | √ |

ARM 的 Classic 系列处理器具有经济实惠的特点，其可以以多种形式进行授权，可以提供单次使用许可、多年期许可和永久使用许可，并且其中多种处理器可用作硬核从而可以降低设计风险并且缩短上市时间。在 ARM 的 Connected Community（合作伙伴联盟）中有 650 多家成员支持 ARM 处理器，提供了专门针对 ARM 指令集架构的关键开放源项目；提供了行业中最广泛的编译器、调试器和 RTOS 工具体系，以及大量可以和处理器集成的第三方 IP。

ARM 的 Classic 系列处理器的比较说明如表 2 所示，需要说明的是，在最新的设计中都推荐使用 Cortex 产品进行升级替换。

表 2：ARM 的 Classic 系列处理器的比较说明

| 系列 | 型号 | 说明 | Cortex 系列替代产品 |
| ARM11 | ARM11MPCore | 率先采用了多核技术，主要应用于手机、导航设备及智能本 | Cortex-A9 Cortex-A5 |
| ARM1176JZ(F)-S | 是 Classic 系列中性能最高的单核处理器，引入了 TrustZone 技术，主要应用于手机、机顶盒、数字电视等 | Cortex-A9 Cortex-A8
Cortex-A5 |
| ARM1156T2(F)-S | 是 Classic 系列中性能最高的实时处理器，首次引入了 Thumb-2 指令集架构，主要应用于高性能确定性控制系统，如汽车、工业控制和机器人等 | Cortex-R4 |
| ARM1136J(F)-S | 其与 ARM926EJ-S 类似，但是扩展了流水线，提升了频率和性能，还引入了基本 SIMD（单指令多数据），可以提高编码、解码的性能，并且可以提供可选浮点支持 | Cortex-A5 |
| ARM9 | ARM968E-S | 是面积最小、功耗最低的 ARM9 处理器，并且可以轻松地通过标准接口集成紧密耦合内存 | Cortex-R4 |
| ARM946E-S | 是包含了可选 cache 接口并且有完整的内存保护单元的实时处理器 | Cortex-R4 |
| ARM926EJ-S | 是入门级的处理器，但是可以支持包括 Linux、Windows CE 和 Symbian 在内的完全版操作系统，所以是需要完整图形用户界面的应用的理想选择 | Cortex-A5 |
| ARM7 | ARM7TDMI-S | 是出色的重负荷处理器，曾经被用于手机，现在广泛地用于移动和非移动领域 | Cortex-M3 Cortex-M0 |