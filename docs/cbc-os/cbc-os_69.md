# 关于操作系统体系结构的习题

提示：以下习题涉及教程 1.8 节及其前面的内容，请至少阅读完 1.8 节后再浏览习题。本小节只有一个选择题。

## 单项选择题

相对于传统操作系统结构，釆用微内核结构设计和实现操作系统具有诸多好处，下列哪些是微内核结构的特点（ ）。

Ⅰ.使系统更高效    Ⅱ.添加系统服务时，不必修改内核

Ⅲ.微内核结构没有单一内核稳定    Ⅳ.使系统更可靠

A. Ⅰ、Ⅲ、Ⅳ    B. Ⅰ、Ⅱ、Ⅳ    C. Ⅱ、Ⅳ    D. Ⅰ、Ⅳ

## 答案与解析

C

微内核结构将操作系统的很多服务移动到内核以外（如文件系统），且服务之间使用进程间通信机制进行信息交换，这种通过进程间通信机制进行信息交换影响了系统的效率，所以 I 是错误的。由于内核的内服务变少了，且一般来说内核的服务越少内核越稳定，所以是Ⅲ错误的。而Ⅱ、Ⅳ正是微内核结构的优点。