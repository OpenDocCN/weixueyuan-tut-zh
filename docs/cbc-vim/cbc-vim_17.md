# vi 字符串检索（查找）命令

vi 编辑器提供简单的字符串检索功能。在末行模式下，用户可以使用不同的命令对需要的字符串进行查找。查找命令有 5 种。

## 1\. / 命令

/命令的格式为：

/string

/命令从光标处开始向后寻找字符串 string。

## 2\. ? 命令

? 命令的格式为：

?string

? 命令从光标处开始向前寻找字符串 string。

## 3\. n 命令

n 命令重复上一条检索命令。

## 4\. N 命令

N 命令重复上一条检索命令，但检索方向改变。例如上次的检索命令是向前检索，那么此次检索的方向是向后；如果上次的检索命令是向后检索，那么此次检索的方向是向前。

## 5\. g/ 命令

g/ 命令的格式是为：

g/string

检索 string。`g/`命令使光标停止在第一个检索到的 string 串的行首。下面实例演示了`g/`命令的用法。

1) 在 vi 编辑器中编辑以下代码：

#include <stdio.h>
int add(int a, int b)
{
    return a + b;
}
int main(void)
{
    int i, sum;
    for (i = 0; i < 3; i++){
        sum = add(i, i + 1);
        printf("%d\n", sum);
    }
}

2) 在末行模式下输入 g/main，显示如下：

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
~
~
~
~
~
:g/main
int main(void)
请按 ENTER 或其它命令继续

按下回车键，光标将移动到`int main(void)`的行首。