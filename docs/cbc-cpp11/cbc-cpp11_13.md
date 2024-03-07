# C++11 for 循环（基于范围的循环）详解

> 原文：[`c.biancheng.net/view/7759.html`](http://c.biancheng.net/view/7759.html)

C++ 11 标准之前（C++ 98/03 标准），如果要用 for 循环语句遍历一个数组或者容器，只能套用如下结构：

for(表达式 1; 表达式 2; 表达式 3){
    //循环体
}

例如，下面程序演示了用上述结构遍历数组和容器的具体实现过程（实例一）：

```

#include <iostream>
#include <vector>
#include <string.h>
using namespace std;

int main() {
    char arc[] = "http://c.biancheng.net/cplus/11/";
    int i;
    //for 循环遍历普通数组
    for (i = 0; i < strlen(arc); i++) {
        cout << arc[i];
    }
    cout << endl;

    vector<char>myvector(arc,arc+23);
    vector<char>::iterator iter;
    //for 循环遍历 vector 容器
    for (iter = myvector.begin(); iter != myvector.end(); ++iter) {
        cout << *iter;
    }
    return 0;
}
```

程序执行结果为：

http://c.biancheng.net/cplus/11/
http://c.biancheng.net/

> 此示例中，vector 为 STL 标准库提供的序列式容器，关于该容器的具体用法，可阅读《C++ STL vector 容器详解》一节，这里不再做重复赘述。

而 C++ 11 标准中，除了可以沿用前面介绍的用法外，还为 for 循环添加了一种全新的语法格式，如下所示：

for (declaration : expression){
    //循环体
}

其中，两个参数各自的含义如下：

*   declaration：表示此处要定义一个变量，该变量的类型为要遍历序列中存储元素的类型。需要注意的是，C++ 11 标准中，declaration 参数处定义的变量类型可以用 auto 关键字表示，该关键字可以使编译器自行推导该变量的数据类型。
*   expression：表示要遍历的序列，常见的可以为事先定义好的普通数组或者容器，还可以是用 {} 大括号初始化的序列。

可以看到，同 C++ 98/03 中 for 循环的语法格式相比较，此格式并没有明确限定 for 循环的遍历范围，这是它们最大的区别，即旧格式的 for 循环可以指定循环的范围，而 C++11 标准增加的 for 循环，只会逐个遍历 expression 参数处指定序列中的每个元素。

下面程序演示了如何用 C++ 11 标准中的 for 循环遍历实例一定义的 arc 数组和 myvector 容器：

```

#include <iostream>
#include <vector>
using namespace std;

int main() {
    char arc[] = "http://c.biancheng.net/cplus/11/";
    //for 循环遍历普通数组
    for (char ch : arc) {
        cout << ch;
    }
    cout << '!' << endl;

    vector<char>myvector(arc, arc + 23);
    //for 循环遍历 vector 容器
    for (auto ch : myvector) {
        cout << ch;
    }
    cout << '!';
    return 0;
}
```

程序执行结果为：

http://c.biancheng.net/cplus/11/ !
http://c.biancheng.net/!

这里有以下 2 点需要说明：
1) 程序中在遍历 myvector 容器时，定义了 auto 类型的 ch 变量，当编译器编译程序时，会通过 myvector 容器中存储的元素类型自动推导出 ch 为 char 类型。注意，这里的 ch 不是迭代器类型，而表示的是 myvector 容器中存储的每个元素。

2) 仔细观察程序的输出结果，其中第一行输出的字符串和 "!" 之间还输出有一个空格，这是因为新格式的 for 循环在遍历字符串序列时，不只是遍历到最后一个字符，还会遍历位于该字符串末尾的 '\0'（字符串的结束标志）。之所以第二行输出的字符串和 "!" 之间没有空格，是因为 myvector 容器中没有存储 '\0'。

除此之外，新语法格式的 for 循环还支持遍历用`{ }`大括号初始化的列表，比如：

```

#include <iostream>
using namespace std;

int main() {
    for (int num : {1, 2, 3, 4, 5}) {
        cout << num << " ";
    }
    return 0;
}
```

程序执行结果为：

1 2 3 4 5

另外值得一提的是，在使用新语法格式的 for 循环遍历某个序列时，如果需要遍历的同时修改序列中元素的值，实现方案是在 declaration 参数处定义引用形式的变量。举个例子：

```

#include <iostream>
#include <vector>
using namespace std;

int main() {
    char arc[] = "abcde";
    vector<char>myvector(arc, arc + 5);
    //for 循环遍历并修改容器中各个字符的值
    for (auto &ch : myvector) {
        ch++;
    }
    //for 循环遍历输出容器中各个字符
    for (auto ch : myvector) {
        cout << ch;
    }
    return 0;
}
```

程序执行结果为：

bcdef

此程序中先后使用了 2 个新语法格式的 for 循环，其中前者用于修改 myvector 容器中各个元素的值，后者用于输出修改后的 myvector 容器中的各个元素。

有读者可能会问，declaration 参数既可以定义普通形式的变量，也可以定义引用形式的变量，应该如何选择呢？其实很简单，如果需要在遍历序列的过程中修改器内部元素的值，就必须定义引用形式的变量；反之，建议定义`const &`（常引用）形式的变量（避免了底层复制变量的过程，效率更高），也可以定义普通变量。