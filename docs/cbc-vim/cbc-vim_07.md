# vi 重复命令（重复上次操作）

当用户在文本编辑中碰到需要机械地重复一些操作的时候，重复命令就派上了用场。它可以让用户方便地再执行一次前面刚完成的某个复杂的命令。重复命令只能在命令模式下工作，在该模式下按下`.`键即可。下面实例演示了使用重复命令。

1) 在 vi 编辑器中编辑以下代码：

#include <stdio.h>
int main (void)
{
    return 0;
}

2) 让光标位于`return 0;`所在的行，输入命令`O`，并输入一行内容后，再按下 Esc 键回到命令模式下，屏幕显示内容为：

#include <stdio.h>
int main(void)
{
    printf ("http://c.biancheng.net\n");
    return 0;
}

3) 此时输入命令`.`，重复上次键入`printf ("http://c.biancheng.net\n");`的操作，屏幕显示内容为：

#include <stdio.h>
int main (void)
{
    printf ("http://c.biancheng.net\n");
    printf ("http://c.biancheng.net\n");
    return 0;
}