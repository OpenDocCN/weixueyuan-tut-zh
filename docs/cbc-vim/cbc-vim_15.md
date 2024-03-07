# vi 文本跨行移动（m 命令）

vi 编辑器提供文本的跨行移动。这个操作允许用户将某一行的文本内容移动到指定位置。

vi 编辑器使用 m 命令实现文本的跨行移动。该命令的使用方法为：

imj

表示把第 i 行移至第 j 行的下方。下面实例演示了 m 命令的使用。

1) 在 vi 编辑器中编辑以下代码：

#include <stdio.h>
int main(void)
{
    printf("hello world\n");
    return 0;
}

2) 在末行模式下输入命令`5m2`，将第 5 行的 printf() 函数调用移动到第 2 行的下方，显示如下：

#include <stdio.h>
int main(void)
    return 0;
{
    printf("hello world\n");
}