# 操作系统的体系结构：大内核和微内核

操作系统的体系结构是一个开放的问题。正如上文所述，操作系统在核心态为应用程序提供公共的服务，那么操作系统在核心态应该提供什么服务、怎样提供服务？有关这个问题的回答形成了两种主要的体系结构：大内核和微内核。

大内核系统将操作系统的主要功能模块都作为一个紧密联系的整体运行在核心态，从而为应用提供高性能的系统服务。因为各管理模块之间共享信息，能有效利用相互之间的有效特性，所以具有无可比拟的性能优势。

但随着体系结构和应用需求的不断发展，需要操作系统提供的服务越来越多，而且接口形式越来越复杂，操作系统的设计规模也急剧增长，操作系统也面临着“软件危机”困境。为此，操作系统设计人员试图按照复杂性、时间常数、抽象级别等因素，将操作系统内核分成基本进程管理、虚存、I/O 与设备管理、IPC、文件系统等几个层次，继而定义层次之间的服务结构，提高操作系统内核设计上的模块化。但是由于层次之间的交互关系错综复杂，定义清晰的层次间接口非常困难，复杂的交互关系也使得层次之间的界限极其模糊。

为解决操作系统的内核代码难以维护的问题，于是提出了微内核的体系结构。它将内核中最基本的功能（如进程管理等）保留在内核，而将那些不需要在核心态执行的功能移到用户态执行，从而降低了内核的设计复杂性。而那些移出内核的操作系统代码根据分层的原则被划分成若干服务程序，它们的执行相互独立，交互则都借助于微内核进行通信。

微内核结构有效地分离了内核与服务、服务与服务，使得它们之间的接口更加清晰，维护的代价大大降低，各部分可以独立地优化和演进，从而保证了操作系统的可靠性。

微内核结构的最大问题是性能问题，因为需要频繁地在核心态和用户态之间进行切换，操作系统的执行开销偏大。因此有的操作系统将那些频繁使用的系统服务又移回内核，从而保证系统性能。但是有相当多的实验数据表明，体系结构不是引起性能下降的主要因素，体系结构带来的性能提升足以弥补切换开销带来的缺陷。为减少切换开销，也有人提出将系统服务作为运行库链接到用户程序的一种解决方案，这样的体系结构称为库操作系统。