# vi 文本移动命令（文本行移动）

在 vi 编辑器中可以方便地将某个范围内的文本行左右移动或从一个地方移至另外一个地方。文本行左右移动的命令有 4 个。下面分小节详细介绍。

## 1\. > 命令

`>`命令将指定的正文行向右移动，通常是右移 8 个空格。移动正文行的范围由光标所在行和随后输入的光标移动命令所限定。

> 命令的使用格式如下：

>n

n 表示光标所在位置后面的文本行数。也可以不写 n，此时默认为 1；换句话说，只使用`>`命令将会移动光标所在的文本行，以及下一个文本行。

如果只想移动光标所在的文本行，可以写`>0`。

需要注意的是，指定的数字是需要移动的行的数目，并不是要右移的空格数。下面实例演示了将一段代码整体右移。

1) 在 vi 编辑器中编辑下面代码，将光标定位到第二行，屏幕显示如下：

#include <stdio.h>
int main(void)
{
    int a = 1, b = 2, add, sub, mul, div;
    add = a + b;
    sub = a - b;
    mul = a * b;
    div = a / b;
    printf("add %d, sub %d, mul %d, div %d\n", add, sub, mul, div);
    return 0;
}

2) 输入`>6`回车后，屏幕显示为：

#include <stdio.h>
        int main(void)
        {
            int a = 1, b = 2, add, sub, mul, div;
            add = a + b;
            sub = a - b;
            mul = a * b;
            div = a / b;
    printf("add %d, sub %d, mul %d, div %d\n", add, sub, mulf div);
    return 0;
}

## 2\. < 命令

`<`命令将指定的正文行向左移动。其使用方式与命令相同，但是方向相反。

## 3\. >> 命令

`>>`该命令将光标所在行右移 8 个空格。如果在`>>`命令之前给出一个数字 n，例如 4，则表示光标当前行及其下面的 3 行（共 4 行）都右移 8 个空格。下面实例演示了使用`>>`命令移动文本。

1) 在 vi 编辑器中编辑以下代码，并且将光标停留在第 4 行上:

#include <stdio.h>
int main(void)
{
int a = 1, b = 2, add, sub, mul, div;
add = a + b;
sub = a - b;
mul = a * b;
div = a / b;
printf(nadd %d, sub %d, mul %d, div %d\n", add, sub, mulf div);
return 0;
}

2) 输入`7>>`后，屏幕显示为：

#include <stdio.h>
int main(void)
{
            int a = 1, b = 2, add, sub, mul, div;
            add = a + b;
            sub = a - b;
            mul = a * b;
            div = a / b;
            printf("add %d, sub %d, mul %df div %d\n", add, sub, mul, div);
            return 0;
}

## 4\. << 命令

`<<`命令将光标所在行左移 8 个空格。其使用与`>>`命令相同，但是移动方向相反。