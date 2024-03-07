# C++11 for 循环使用注意事项

> 原文：[`c.biancheng.net/view/7760.html`](http://c.biancheng.net/view/7760.html)

《C++11 for 循环》一节已经详细介绍了 C++11 标准中 for 循环的基本用法。在此基础上，本节将介绍一些 for 循环的使用注意事项，帮助读者更准确高效地使用基于范围的 for 循环。

1) 首先需要明确的一点是，当使用 for 循环遍历某个序列时，无论该序列是普通数组、容器还是用`{ }`大括号包裹的初始化列表，遍历序列的变量都表示的是当前序列中的各个元素。

举个例子：

```

#include <iostream>
#include <vector>
using namespace std;

int main() {
    //for 循环遍历初始化列表
    for (int ch : {1,2,3,4,5}) {
        cout << ch;
    }
    cout << endl;
    //for 循环遍历普通数组
    char arc[] = "http://c.biancheng.net/cplus/11/";
    for (char ch : arc) {
        cout << ch;
    }
    cout << endl;
    //for 循环遍历 vector 容器
    vector<char>myvector(arc, arc + 23);
    for (auto ch : myvector) {
        cout << ch;
    }
    return 0;
}
```

程序执行结果为：

12345
http://c.biancheng.net/cplus/11/
http://c.biancheng.net/

上面程序演示了用 for 循环遍历 3 种序列的过程，其中前两种情况很容易理解，但对于用基于范围的 for 循环遍历容器中的元素，很多读者会将 ch 误认为指向各个元素的迭代器，其实不然，它表示的仍是容器中的各个元素。

为了加深读者对遍历容器的理解，下面程序以 map 容器为例，再举一个例子：

```

#include <iostream>
#include <map>
#include <string>
using namespace std;

int main() {
    map<string, string>mymap{ {"C++11","http://c.biancheng.net/cplus/11/"},
                              {"Python","http://c.biancheng.net/python/"},
                              {"Java","http://c.biancheng.net/java/"} };
    for (pair<string,string> ch : mymap) {
        cout << ch.first << " " << ch.second << endl;
    }
    return 0;
}
```

程序执行结果为：

C++11 http://c.biancheng.net/cplus/11/
Java http://c.biancheng.net/java/
Python http://c.biancheng.net/python/

要知道，map 容器中存储的不再是普通数据类型的数据，而是 pair 类型的数据，因此程序中在使用基于范围的 for 循环遍历 map 容器时，定义的是 pair 类型的变量。

值得初学者注意的一点是，基于范围的 for 循环也可以直接遍历某个字符串，比如：

```

for (char ch : "http://c.biancheng.net/cplus/11/") {
    cout << ch;
}
```

前面提到，普通数组可以作为被遍历的序列。拿此程序中的字符串来说，其数据类型为`const char[33]`，即在编译器看来字符串就是一个普通数组，因此完全可以直接作为被遍历的序列。

> 当然，基于范围的 for 循环也可以遍历 string 类型的字符串，这种情况下冒号前定义 char 类型的变量即可。

2) 总的来说，基于范围的 for 循环可以遍历普通数组、string 字符串、容器以及初始化列表。除此之外，for 循环冒号后还可以放置返回 string 字符串以及容器对象的函数，比如：

```

#include <iostream>
#include <vector>
#include <string>
using namespace std;

string str = "http://c.biancheng.net/cplus/11/";
vector<int> myvector = { 1,2,3,4,5 };

string retStr() {
    return str;
}

vector<int> retVector() {
    return myvector;
}

int main() {
    //遍历函数返回的 string 字符串
    for (char ch : retStr()) {
        cout << ch;
    }
    cout << endl;
    //遍历函数返回的 vector 容器
    for (int num : retVector()) {
        cout << num << " ";
    }
    return 0;
}
```

程序执行结果为：

http://c.biancheng.net/cplus/11/
1 2 3 4 5

注意，基于范围的 for 循环不支持遍历函数返回的以指针形式表示的数组，比如：

```

//错误示例
#include <iostream>
using namespace std;

char str[] = "http://c.biancheng.net/cplus/11/";

char* retStr() {
    return str;
}

int main() {
    for (char ch : retStr()) //直接报错
    {
        cout << ch;
    }
    return 0;
}
```

原因很简单，此格式的 for 循环只能遍历有明确范围的一组数据，上面程序中 retStr() 函数返回的是指针变量，遍历范围并未明确指明，所以编译失败。

3) 值得一提的是，当基于范围的 for 循环遍历的是某函数返回的 string 对象或者容器时，整个遍历过程中，函数只会执行一次。

举个例子：

```

#include <iostream>
#include <string>
using namespace std;

string str= "http://c.biancheng.net/cplus/11/";

string retStr() {
    cout << "retStr:" << endl;
    return str;
}

int main() {
    //遍历函数返回的 string 字符串
    for (char ch : retStr()) {
        cout << ch;
    }
    return 0;
}
```

程序执行结果为：

retStr:
http://c.biancheng.net/cplus/11/

借助执行结果不难分析出，整个 for 循环遍历 str 字符串对象的过程中，retStr() 函数仅在遍历开始前执行了 1 次。

4) 系统学过 STL 标准库的读者应该知道，基于关联式容器（包括哈希容器）底层存储机制的限制：

1.  不允许修改 map、unordered_map、multimap 以及 unordered_multimap 容器存储的键的值；
2.  不允许修改 set、unordered_set、multiset 以及 unordered_multiset 容器中存储的元素的值。

> 关于以上各个容器的具体用法，读者可猛击《C++ STL 教程》进行系统学习。

因此，当使用基于范围的 for 循环遍历此类型容器时，切勿修改容器中不允许被修改的数据部分，否则会导致程序的执行出现各种 Bug。

另外，基于范围的 for 循环完成对容器的遍历，其底层也是借助容器的迭代器实现的。举个例子：

```

#include <iostream>
#include <vector>
int main(void)
{
    std::vector<int>arr = { 1, 2, 3, 4, 5 };
    for (auto val : arr)
    {
        std::cout << val << std::endl;
        arr.push_back(10); //向容器中添加元素
    }
    return 0;
}
```

程序执行结果可能为（输出结果不唯一）：

1
-572662307
-572662307
4
5

可以看到，程序的执行结果并不是我们想要的。就是因为在 for 循环遍历 arr 容器的同时向该容器尾部添加了新的元素（对 arr 容器进行了扩增），致使遍历容器所使用的迭代器失效，整个遍历过程出现错误。

> 如果读者想要彻底搞清楚程序执行失败的原因，读了解 vector 容器的底层存储机制，可阅读《C++ vector 容器底层实现机制》一文。

因此，在使用基于范围的 for 循环遍历容器时，应避免在循环体中修改容器存储元素的个数。