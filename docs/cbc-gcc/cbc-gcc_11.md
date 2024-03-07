# GCC -c 选项：只编译不链接，仅生成目标文件

`-c`选项表示编译、汇编指定的源文件（也就是编译源文件），但是不进行链接。使用`-c`选项可以将每一个源文件编译成对应的目标文件。

目标文件是一种中间文件或者临时文件，如果不设置该选项，gcc 一般不会保留目标文件，可执行文件生成完成后就自动删除了。

下面实例演示了 gcc -c 选项的用法。

```
$gcc -c test1.c test2.c test3.c
$ls -l *.o
-rwxr--r--  1  root  23  Feb  7  02:57  test1.o
-rwxr--r--  1  root  17  Feb  7  02:57  test2.o
-rwxr--r--  1  root  20  Feb  7  02:57  test3.o
```

如果不使用`-c`选项，则仅仅生成一个可执行文件，没有目标文件。

注意，使用`-c`选项表示只编译源文件，而不进行链接，因此，对于链接中的错误是无法发现的。

下面例子演示了 gcc 编译器在使用`-c`选项的时候不会发现链接错误。

1) 编写如下的两个源文件。

在 func.c 中定义了 func_a() 函数：

```
#include <stdio.h>
void func_a(){
    printf("FUNC_A\n");
}
```

在 main.c 中调用了 func_a() 和 func_b() 函数：

```
#include <stdio.h>
int main(void)
{
    func_a();
    func_b();
    return 0;
}
```

func_b() 函数并没有定义，所以在链接时会产生错误（编译时不会产生错误）。

2) 使用`-c`选项编译两个源文件，如下所示：

$gcc -c func.c main.c

编译器没有输出任何错误信息。

3) 不使用`-c`选项编译两个源文件：

$gcc func.c main.c

会看到如下的报错信息：

/tmp/ccLlOhvh.o：在函数‘main’中：
main.c:(.text+0x14)：对‘func_b’未定义的引用
collect2: 错误：ld 返回 1

由于没有找到 func_b() 函数的定义，所以发生了链接错误。