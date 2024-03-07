# Tkinter Toplevel 控件

> 原文：[`www.weixueyuan.net/a/634.html`](http://www.weixueyuan.net/a/634.html)

Toplevel widget 用于创建一个独立窗口，此独立窗口可以不必有父控件。Toplevel 控件拥有与 tkinter.Tk() 方法所打开窗口的所有特性，同时还拥有以下方法：

#### 1) deiconify()

在使用 iconify() 或 withdraw() 方法后，显示该窗口。

#### 2) frame()

返回一个系统特定的窗口识别码。

#### 3) group(window)

将此窗口加入 window 窗口群组中。

#### 4) iconify()

将窗口缩小成小图标。

#### 5) protocol(name, function)

将 function 函数登记为 callback 函数。

#### 6) state()

返回目前窗口的状态，可以是 normal、iconic、withdrawn、或 icon。

#### 7) transient([master])

将此窗口转换为 master 或父窗口的暂时窗口。当 master 变成小图标时，此窗口也会跟之隐藏起来。

#### 8) withdraw()

将此窗口从屏幕上关闭，但不删除它。

以下方法用于存取窗口的特定信息。

存取窗口的特定信息方法

| 方法名称 | 说明 |
| aspect(minNumber, minDenom, masNumber, masDenom) | 设置窗口宽度与长度的比值，此比值必须在 minNumber / minDenom 与 masNumber / masDenom 之间。如果忽略这些参数，则返回这 4 个值的元组。 |
| client(name) | 使用在 X window 系统中，用于定义 WM_CLIENT_MACHINE 属性。 |
| colormapwindows(wlist...) | 使用在 X window 系统中，用于定义 WM_COLORMAP_WINDOWS 属性。 |
| command(value) | 使用在 X window 系统，用于定义 WM_COMMAND 属性。 |
| focusmodel(model) | 设置焦点模型。 |
| geometry(geometry) | 使用"widthxheight+xoffset+yoffset"格式改变窗口的几何设置。 |
| iconbitmap(bitmap) | 定义窗口变成小图标时，所使用的单色位图图标。 |
| iconmask(bitmap) | 定义窗口变成小图标时，所使用的单色位图屏蔽 |
| iconname(newName=None) | 定义窗口变成小图标时，所使用的图标名称。 |
| iconposition(x, y) | 定义窗口变成小图标时，窗口的 x、y 位置。 |
| iconwindow(window) | 定义窗口变成小图标时，所使用的图标窗口。 |
| maxsize(width, height) | 定义窗口大小的最大值 |
| minsize(width, height) | 定义窗口大小的最小值。 |
| overrideredirect(flag) | 定义一个非零的标志。 |
| position(who) | 定义位置控制器。 |
| resizable(width, height) | 定义是否可以改变窗口大小的标志。 |
| sizefrom(who) | 定义大小控制器。 |
| title(string) | 定义窗口的标题。 |