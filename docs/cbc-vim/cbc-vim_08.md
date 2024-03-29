# vi 退出命令

准备退出 vi 的方式有多种方法，下面进行详细介绍。

## 1\. 命令 q

在末行模式下，输入命令如下：

:q

如果退出时当前编辑文件尚未保存，则 vi 并不退出，而是继续等待用户的命令，并且会在显示窗口的最末行说明如下信息：

No write since last change (use! to overrides)

说明：当用户不清楚自己当前编译的文件是否被修改时，可以使用该命令进行测试，而不必担心因为误操作导致文件数据丢失。

## 2\. 命令 q!

在末行模式下，输入命令如下：

:q!

该命令不论文件是否改变都会强行退出 vi 编辑器，对于此命令用户应当慎用。

## 3\. 命令 w

在末行模式下，输入命令如下：

:w  新文件名

vi 保存当前编辑文件，但并不退出，而是继续等待用户输入命令。在使用 w 命令时，可以再给当前编辑文件起一个新的文件名。这个功能相当于将该文件另存为为一个新的文件。下面实例演示了将 main.c 文件另存为 test.c 文件。

#include "common.h"
int main(void)
{
    int a, b;
    sacnf("%d %d", &af &b);
    swap (a, b);
    printf ("%d, %d\n", a, b);
    return 0;
}
:w test.c

保存之后使用`:q`命令退出 vi 编辑器，使用 ls 命令查看当前目录下的文件时，会发现多了一个 test.c 文件，该文件的内容和 main.c 一致。可以使用 cat 命令查看该文件的内容，main.c 文件的内容不受影响。若指定的新文件是一个已存在的文件，则 vi 在显示窗口的状态行给出如下说明信息：

File exists (use! to override)

说明：这样做同样可以防止因误操作覆盖已经存在的文件，用户可以选择另外的文件名来保存当前文件。

## 4\. 命令 w!

在末行模式下，输入命令如下：

:w!  新文件名

说明：该命令与`:w`命令相同，所不同的是，即使指定的新文件存在，vi 编辑器也会用当前编辑文件对其进行替换，而不再询问用户。因此，此命令同样要慎用。

## 5\. 命令 wq

在末行模式下，输入命令如下：

:wq

vi 将先保存文件，然后退出 vi 返回到 shell。如果当前文件尚未取名，则需要现指定一个文件名。

## 6\. 命令 x

在末行模式下，输入命令如下：

:x

说明：若当前编辑文件曾被修改过，则 vi 会保存该文件。否则 vi 直接退出，不保存该文件。