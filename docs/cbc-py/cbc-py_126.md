# Python GUI 库大汇总

前面介绍的所有程序都是基于命令行的，这些程序可能只有一些“专业”的计算机人士才会使用。例如前面编写的五子棋等程序，恐怕只有程序员自己才愿意玩这么“糟糕”的游戏，很少有最终用户愿意对着黑乎乎的命令行界面敲命令。

相反，如果为程序提供直观的图形用户界面（Graphics User Interface, GUI），最终用户通过拖动鼠标、单击等动作就可以操作整个应用，这样的应用程序就会很受政迎（实际上，Windows 之所以广为人知，其最初的吸引力就是来自它所提供的图形用户界面）。

作为一个程序设计者，必须优先考虑用户的感受，一定要让用户感到“爽”，程序才会被需要、被使用，这样的程序才有价值。

在真正开始介绍 Python 图形界面编程之前，首先简单介绍一下 Python 的图形用户界面库。

#### 1) PyGObject

PyGObject 库为基于 GObject 的 C 函数库提供了内省绑定，这些库可以支持 GTK+3 图形界面工具集，因此时 GObject 提供了丰富的图形界面组件。

#### 2) PyGTK

PyGTK 基于老版本的 GTK+2 的库提供绑定，借助于底层 GTK+2 所提供的各种可视化元素和组件，同样可以开发出在 GNOME 桌面系统上运行的软件，因此它主要适用于 Linux/UNIX 系统。PyGTK 对 GTK+2 的 C 语言进行了简单封装，提供了面向对象的编程接口。其官方网址是[`www.pygtk.org/`](http://www.pygtk.org/)。

#### 3) PyQt

PyQt 是 Python 编程语言和 Qt 库的成功融合。Qt 本身是一个扩展的 C++ GUI 应用开发框架，Qt 可以在 UNIX、Windows 和 Mac OS X 上完美运行，因此 PyQt 是建立在 Qt 基础上的 Python 包装。所以 PyQt 也能跨平台使用。

#### 4) PySide

PySide 是由 Nokia 提供的对 Qt 工具集的新的包装库，目前成熟度不如 PyQt。

#### 5) wxPython

wxPython 是一个跨平台的 GUI 工具集，wxPython 以流行的 wxWidgets（原名 wxWindows）为基础，提供了良好的跨平台外观。简单来说，wxPython 在 Windows 上调用 Windows 的本地组件、在 Mac OS 上调用 Mac OS X 的本地组件、在 Linux 上调用 Linux 的本地组件，这样可以让 GUI 程序在不同的平台上显示平台对应的风格。wxPython 是一个非常流行的跨平台的 GUI 库。其官方网址是[`www.wxpython.org/`](http://www.wxpython.org/)。

如果读者有需要，则完全可以选择上面这些 Python GUI 库来开发图形用户界面。如果考虑开发跨平台的图形用户界面，则推荐使用 PyQt 或 wsPython。

本章所介绍的 GUI 库是 Tkinter，它是 Python 自带的 GUI 库，无须进行额外的下载安装，只要导入 tkinter 包即可使用。