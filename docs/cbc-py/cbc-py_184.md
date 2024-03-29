# 什么是线程（多线程），Python 多线程的好处（超级详细）

几乎所有的操作系统都支持同时运行多个任务，一个任务通常就是一个程序，每一个运行中的程序就是一个进程。当一个程序运行时，内部可能包含多个顺序执行流，每一个顺序执行流就是一个线程。

## 线程和进程

几乎所有的操作系统都支持进程的概念，所有运行中的任务通常对应一个进程（Process）。当一个程序进入内存运行时，即变成一个进程。进程是处于运行过程中的程序，并且具有一定的独立功能。进程是系统进行资源分配和调度的一个独立单位。

一般而言，进程包含如下三个特征：

1.  独立性：进程是系统中独立存在的实体，它可以拥有自己的独立的资源，每一个进程都拥有自己的私有的地址空间。在没有经过进程本身允许的情况下，一个用户进程不可以直接访问其他进程的地址空间。
2.  动态性：进程与程序的区别在于，程序只是一个静态的指令集合，而进程是一个正在系统中活动的指令集合。在进程中加入了时间的概念。进程具有自己的生命周期和各种不同的状态，在程序中是没有这些概念的。
3.  并发性：多个进程可以在单个处理器上并发执行，多个进程之间不会互相影响。

并发（Concurrency）和并行（Parallel）是两个概念，并行指在同一时刻有多条指令在多个处理器上同时执行；并发才旨在同一时刻只能有一条指令执行，但多个进程指令被快速轮换执行，使得在宏观上具有多个进程同时执行的效果。

大部分操作系统都支持多进程并发执行，现代的操作系统几乎都支持同时执行多个任务。例如，程序员一边开着开发工具在写程序，一边开着参考手册备查，同时还使用电脑播放音乐……除此之外，每台电脑运行时还有大量底层的支撑性程序在运行……这些进程看上去像是在同时工作。

但事实的真相是，对于一个 CPU 而言，在某个时间点它只能执行一个程序。也就是说，只能运行一个进程，CPU 不断地在这些进程之间轮换执行。那么，为什么用户感觉不到任何中断呢？

这是因为相对人的感觉来说，CPU 的执行速度太快了（如果启动的程序足够多，则用户依然可以感觉到程序的运行速度下降了）。所以，虽然 CPU 在多个进程之间轮换执行，但用户感觉到好像有多个进程在同时执行。

现代的操作系统都支持多进程的并发执行，但在具体的实现细节上可能因为硬件和操作系统的不同而采用不同的策略。比较常用的策略有：

*   共用式的多任务操作策略，例如 Windows 3.1 和 Mac OS 9 操作系统采用这种策略；
*   抢占式的多任务操作策略，其效率更高，目前操作系统大多采用这种策略，例如 Windows NT、Windows 2000 以及 UNIX/Linux 等操作系统。

多线程则扩展了多进程的概念，使得同一个进程可以同时并发处理多个任务。线程（Thread）也被称作轻量级进程（Lightweight Process），线程是进程的执行单元。就像进程在操作系统中的地位一样，线程在程序中是独立的、并发的执行流。

当进程被初始化后，主线程就被创建了。对于绝大多数的应用程序来说，通常仅要求有一个主线程，但也可以在进程内创建多个顺序执行流，这些顺序执行流就是线程，每一个线程都是独立的。

线程是进程的组成部分，一个进程可以拥有多个线程，一个线程必须有一个父进程。线程可以拥有自己的堆栈、自己的程序计数器和自己的局部变量，但不拥有系统资源，它与父进程的其他线程共享该进程所拥有的全部资源。因为多个线程共享父进程里的全部资源，因此编程更加方便；但必须更加小心，因为需要确保线程不会妨碍同一进程中的其他线程。

线程可以完成一定的任务，可以与其他线程共享父进程中的共享变量及部分环境，相互之间协同未完成进程所要完成的任务。

线程是独立运行的，它并不知道进程中是否还有其他线程存在。线程的运行是抢占式的，也就是说，当前运行的线程在任何时候都可能被挂起，以便另外一个线程可以运行。

一个线程可以创建和撤销另一个线程，同一个进程中的多个线程之间可以并发运行。

从逻辑的角度来看，多线程存在于一个应用程序中，让一个应用程序可以有多个执行部分同时执行，但操作系统无须将多个线程看作多个独立的应用，对多线程实现调度和管理，以及资源分配。线程的调度和管理由进程本身负责完成。

简而言之，一个程序运行后至少有一个进程，在一个进程中可以包含多个线程，但至少要包含一个主线程。

归纳起来可以这样说，操作系统可以同时执行多个任务，每一个任务就是一个进程，进程可以同时执行多个任务，每一个任务就是一个线程。

## 多线程的好处

线程在程序中是独立的、并发的执行流。与分隔的进程相比，进程中线程之间的隔离程度要小，它们共享内存、文件句柄和其他进程应有的状态。

因为线程的划分尺度小于进程，使得多线程程序的并发性高。进程在执行过程中拥有独立的内存单元，而多个线程共享内存，从而极大地提高了程序的运行效率。

线程比进程具有更高的性能，这是由于同一个进程中的线程都有共性多个线程共享同一个进程的虚拟空间。线程共享的环境包括进程代码段、进程的公有数据等，利用这些共享的数据，线程之间很容易实现通信。

操作系统在创建进程时，必须为该进程分配独立的内存空间，并分配大量的相关资源，但创建线程则简单得多。因此，使用多线程来实现并发比使用多进程的性能要高得多。

总结起来，使用多线程编程具有如下几个优点：

1.  进程之间不能共享内存，但线程之间共享内存非常容易。
2.  操作系统在创建进程时，需要为该进程重新分配系统资源，但创建线程的代价则小得多。因此，使用多线程来实现多任务并发执行比使用多进程的效率高。
3.  Python 语言内置了多线程功能支持，而不是单纯地作为底层操作系统的调度方式，从而简化了 Python 的多线程编程。

在实际应用中，多线程是非常有用的。比如一个浏览器必须能同时下载多张图片；一个 Web 服务器必须能同时响应多个用户请求；图形用户界面（GUI）应用也需要启动单独的线程，从主机环境中收集用户界面事件……总之，多线程在实际编程中的应用是非常广泛的。