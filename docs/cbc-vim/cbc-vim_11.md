# vi 按行移动光标

在 vi 编辑器中，光标可以按行移动，一次性移动一行或者多行。

## 1\. H 命令

H 命令将光标移至当前屏幕的第 1 行，而不是整个文件的第 1 行。若在 H 命令之前加 上数字 n，则将光标移至第 n 行的行首。下面实例演示了使用 H 命令快速移动光标。

1) 在 vi 编辑器中编辑以下代码：

#include <stdio.h>
int add(int a, int b)
{
    return a + b;
}
int main (void)
{
    int i;
    int sum;
    for (i = 0; i < 3; i++){
        sum = add(i, i + 1);
        printf("%d\n", sum);
    }
}

2) 在命令行模式下，输入 H 命令，光标位于第一行开头，如下所示：

#include <stdio.h>
int add(int a, int b)
{
    return a + b;
}
int main (void)
{
    int i;
    int sum;
    for (i = 0; i < 3; i++){
        sum = add(i, i + 1);
        printf("%d\n", sum);
    }
}

3) 在命令模式下输入 4H 命令后，光标移到第 4 行的 return 的`r`字符之上：

#include <stdio.h>
int add(int a, int b)
{
    return a + b;
}
int main (void)
{
    int i;
    int sum;
    for (i = 0; i < 3; i++){
        sum = add(i, i + 1);
        printf("%d\n", sum);
    }
}

## 2\. M 命令

M 命令将光标移至屏幕显示文件的中间行的行首。即如果当前屏幕已经充满，则移动到整个屏幕的中间行；否则，移动到显示文本的那些行的中间行。

利用此命令可以快速地将光标从屏幕的任意位置移至屏幕显示文件的中间行的行首。

## 3\. L 命令

L 命令将光标移至屏幕显示文件的最底行的行首。即如果文件内容需要超过一个屏幕显示时，该命令将光标移至屏幕上最底行的行首；否则该命令将光标移至文件最后一行的行首。可见，利用此命令可以快速准确地将光标移至屏幕底部或文件的最后一行。

若在 L 命令之前加上数字 n，则将光标移至从屏幕底部算起第 n 行的行首。

## 4\. G 命令

在全文的范围内移动光标，此时的光标移动范围不再受屏幕的限制。例如，在上例中，在命令模式下输入命令 5G 之后光标将移到第 5 行的行首。