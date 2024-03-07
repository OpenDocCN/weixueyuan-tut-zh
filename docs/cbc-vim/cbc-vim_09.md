# vi 字符替换命令（r 和 R 命令）

vi 编辑器可以对文本进行替换操作，其命令如下。

## 1\. r 命令

该命令将当前光标所指的字符替换为提供的字符。可以在该命令之前加上数字 n，表示将从当前字符开始的 n 个字符替换为提供的字符。下面实例演示了 r 命令的使用。

1) 在 vi 编辑器中编辑以下代码：

#include <stdio.h>
int add(int a, int b)
{
    return a + b;
}
int main(void)
{
    int i, sum;
    for (i = 0; i < 3 ; i++){
        sum = add(i, i + 1);
        printf("%d\n", sum);
    }
}
"test.c" 13L, 170C

2) 将光标移至 main 的首字符`a`上，在命令行模式下输入 4rb，显示如下：

#include <stdio.h>
int add(int a, int b)
{
    return a + b;
}
int mbbbbvoid)
{
    int i, sum;
    for (i = 0; i < 3; i++){
        sum = add(i, i + 1);
        printf("%d\n", sum);
    }
}
"test.c" 13L, 170C

## 2\. R 命令

该命令会让 vi 进入 replace 模式。在此模式下，每个输入的字符都会替换当前光标下的字符，直到输入 <Esc> 结束该模式。