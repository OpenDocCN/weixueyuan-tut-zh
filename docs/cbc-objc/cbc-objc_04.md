# 分析第一个 OC 程序

下面我们来分析一下上节中给出的 OC 代码，让大家对 OC 的结构有所了解。代码如下：

```
#import <Foundation/Foundation.h>

int main() {
    NSLog(@"Hello, World!");
    return 0;
}
```

1) 在 OC 中，`#import`命令用来引入头文件。与 C 语言中的`#include`相比，#import 能够保证头文件只被引入一次，也就是说，只有第一次引入有效，后续对同一文件的引入都会被忽略。

而在 C 语言中，每次使用 #include 命令都会将头文件的内容引入一次，所以在头文件中必须使用`#ifndef`和`#endif`宏来保证头文件只被编译一次，这在《C 语言模块化编程》中进行了详细介绍。现在 OC 中有了 #import，程序员就不用顾虑这些了。

2) `Foundation/Foundation.h`是 Foundation 框架的头文件，只要引入了它，就可以使用 Foundation 框架的所有功能了。

Foundation 是 OC 语言的基础框架，它提供了 OC 专有的基本数据类型（如字符串、数组、时间日期等），提供了多线程、网络连接、文件操作、本地数据存储等常用功能。Foundation.h 就像 C 语言中的 stdio.h，一般都需要引入。

3) main() 是程序的入口函数，这点和 C 语言一样。一个 OC 程序有且只能有一个 main() 函数。更多信息请查看《分析第一个 C 语言程序》。

4) NSLog() 是 OC 中的格式化输出函数，相当于 C 语言中的 printf() 的升级版，不仅可以用来输出 C 语言中的数据，还可以输出 OC 中的数据。

`@`符号没有实际含义，只是用来作为 OC 字符串的特有标志。在 OC 中，字符串前面都要加`@`，如果不加，就变成了 C 语言中的字符串，就要使用字符指针或字符数组来定义，这在后面会详细讲解。

在 NSLog() 中，如果涉及到 C 语言中的基本数据类型，就可以使用%d、%f、%s 等格式控制字符来输出，请看下面的代码：

```
#import <Foundation/Foundation.h>

int main() {
    int a = 100;
    float b = 39.52;
    char *str1 = "Objective-C";  //C 语言中的字符串
    NSString *str2 = @"http://c.biancheng.net";  //OC 中的字符串
    NSLog(@"a: %d\nb: %f\nstr1: %s\n str2: %@\n", a, b, str1, str2);

    return 0;
}
```

运行结果：

a: 100

b: 39.520000

str1: Objective-C

str2: http://c.biancheng.net

NSString 是 OC 中的字符串类型，需要使用格式控制符`%@`来输出。

可以发现，不管是 NSString 类型还是 NSLog() 函数，它们都有一个共同的前缀`NS`。在《Objective-C 简介》中我们讲过，乔布斯离开苹果后创办了自己的公司 NeSt，并开发了 Foundation 框架，该框架中的很多数据类型名、变量名、函数名都是以`NS`开头，乔布斯以此来纪念自己的公司。