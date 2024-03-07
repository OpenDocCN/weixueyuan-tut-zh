# GCC 所支持的平台

GCC 编译程序集合可以在很多平台上运行。平台是指特定计算机芯片（CPU）及其运行的操作系统的组合。

尽管 GCC 已经被移植到数以千计的平台（硬件/软件的组合）上，但只有一些基木平台可以用来测试发布的正确性。表 1 中列出的平台是最流行的，并且它们对 GCC 支持的最好。

表 1：GCC 支持的主要平台

| 硬件 | 操作系统 |
| Alpha | Red Hat Linux 7.1 |
| HPPA | HPUX 11.0 |
| Intel x86 | Debian Linux 2.2、Red Hat Linux 6.2 和 FreeBSD 4.5 |
| MIPS | IRIX 6.5 |
| PowerPC | AIX 4.3.3 |
| Sparc | Solaris 2.7 |

GCC 除了可以保证在表 1 中列出的主要平台上正确运行，也能很好地支持表 2 中列出的次要平台。

表 2：GCC 支持的次要平台

| 硬件 | 操作系统 |
| PowerPC | Linux |
| Sparc | Linux |
| ARM | Linux |
| Intel x86 | Cygwin |

以上平台都是 GCC 进行了良好测试的平台。相比数以千计的平台，这些平台显然很少，这主要是因为人力和资金问题。

即使你的平台没有被列在上面，GCC 还是可能会运行地很好。GCC 的源代码中提供了完整的测试集合，你可以在自己的平台上自行测试。

另一种方法就是作为志愿者来测试你的平台，这样 GCC 就可以在每次发布之前得到测试。