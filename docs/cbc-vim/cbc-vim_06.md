# vi 撤销命令（u 和 U），撤销上一次的操作

撤销命令是非常有用的，它可以撤销前一次的误操作或不合适的操作对文件造成的影响。撤销命令分为以下两种。

*   u 命令：该命令撤销上一次所做的操作。多次使用 u 命令会一步一步依次撤销之前做过的操作（在一次切换到文本输入模式中输入的所有文本算一次操作）。
*   U 命令：该命令会一次性撤销自上次移动到当前行以来做过的所有操作，再使用一次 U 命令则撤销之前的 U 命令所做的操作，恢复被撤销的内容。

下面实例演示了两个命令的区别。

1) 在 vi 编辑器中编辑以下代码：

#include <stdio.h>
int main(void)
{
    printf("hello world\n");
    return 0;
}

2) 光标停留在 h 的左侧，在命令模式下输入命令 o 插入一新行，输入需要插入的内容后再按下 Esc 键回到命令模式，屏幕显示内容如下：

#include <stdio.h>
int main(void)
{
    printf("hello world\n");
    printf("hello real-world\n");
    printf("hello world\n");
    return 0;
}

3) 使用命令 U 后，取消所有操作，屏幕显示为：

#include <stdio.h>
int main(void)
{
    printf("hello world\n");
    return 0;
}

4) 使用命令 u 后，仅取消第 2 个输入的串`printf("hello world\n");`，屏幕显示为：

#include <stdio.h>
int main(void)
{
    printf("hello world\n");
    printf("hello real-world\n");
    return 0;
}