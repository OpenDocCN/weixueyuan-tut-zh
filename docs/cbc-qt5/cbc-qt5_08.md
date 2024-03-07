# Qt 项目中 main 主函数及其作用

main.cpp 是实现 main() 函数的文件，下面是 main.cpp 文件的内容。

```
#include "widget.h"
#include <QApplication>
int main(int argc, char *argv[])
{
    QApplication a(argc, argv); //定义并创建应用程序
    Widget w; //定义并创建窗口
    w.show(); //显示窗口
    return a.exec(); //应用程序运行
}
```

main() 函数是应用程序的入口。它的主要功能是创建应用程序，创建窗口，显示窗口，并运行应用程序，开始应用程序的消息循环和事件处理。

QApplication 是 Qt 的标准应用程序类，第 1 行代码定义了一个 QApplication 类的实例 a，就是应用程序对象。

然后定义了一个 Widget 类的变量 w，Widget 是本实例设计的窗口的类名，定义此窗口后再用 w.show() 显示此窗口。

最后一行用 a.exec() 启动应用程序的执行，开始应用程序的消息循环和事件处理。