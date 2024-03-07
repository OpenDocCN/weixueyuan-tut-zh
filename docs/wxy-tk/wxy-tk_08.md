# Tkinter 事件介绍

> 原文：[`www.weixueyuan.net/a/564.html`](http://www.weixueyuan.net/a/564.html)

有时候在使用 tkinter 创建图形模式应用程序过程中需要处理一些事件，如键盘、鼠标等动作。只要设置好事件处理例程（此函数称为 callback），就可以在控件内处理这些事件。使用语法如下：

```

def function():
    ...
widget.bind("<event>",function)
```

参数的含义如下：

*   widget 是 tkinter 控件的实例变量。
*   <event>是事件的名称。
*   function 是事件处理例程。tkinter 会传给事件处理例程一个 event 变量，此变量内包含事件发生时的 x、y 坐标（鼠标事件）及 ASCII 码（键盘事件）等。

## 1\. tkinter 事件属性

当有事件发生时，tkinter 会传给事件处理例程一个 event 变量，此变量包含以下属性：

#### 1) char

键盘的字符码，如"a"键的 char 属性等于 "a"，F1 键的 char 属性无法显示。

#### 2) keycode

键盘的 ASCII 码，如 "a" 键的 keycode 属性等于 65。

#### 3) keysym

键盘的符号，如 "a" 键的 keysym 属性等于 "a"，F1 键的 keysym 属性等于 "F1"。

#### 4) height,width

控件的新高度与宽度，单位是像素。

#### 5) num

事件发生时的鼠标按键码。

#### 6) widget

事件发生所在的控件实例变量。

#### 7）x,y

目前的鼠标光标位置。

#### 8) x_root,y_root

相对于屏幕左上角的目前鼠标光标位置。

#### 9) type

显示事件的种类。

## 2\. tkinter 事件绑定方法

用户可以使用下面 tkinter 控件的方法，将控件与事件绑定起来。

#### 1) after(milliseconds [, callback [, arguments]])

在 milliseconds 事件后，调用 callback 函数，arguments 是 callback 函数的参数。此方法返回一个 identifier 值，可以应用在 after_cancel() 方法。

#### 2) after_cancel(identifier)

取消 callback 函数，identifier 是 after() 函数的返回值。

#### 3) after_idle(callback, arguments)

当系统在 idle 状态（无事可做）时，调用 callback 函数。

#### 4) bindtags()

返回控件所使用的绑定搜索顺序。返回值是一个元组，包含搜索绑定所用的命名空间。

#### 5) bind(event, callback)

设置 event 事件的处理函数 callback。可以使用 bind(event,callback, "+") 格式设置多个 callback 函数。

#### 6) bind_all(event, callback)

设置 event 事件的处理函数 callback。可以使用 bind_all(event,callback, "+") 格式设置多个 callback 函数。此方法可以设置公用的快捷键。

#### 7) bind_class(widgetclass, event, callback)

设置 event 事件的处理函数 callback，此 callback 函数由 widgetcalss 类而来。可以使用 bind_class(widgetclass, event, callback, "+") 格式设置多个 callback 函数。

#### 8)<Configure>

此实例变量可以用于指示当控件的大小改变，或者移到新的位置。

#### 9) unbind(event)

删除 event 事件与 callback 函数的绑定。

#### 10) unbind_all(event)

删除应用程序附属的 event 事件与 callback 函数的绑定。

#### 11) unbind_class(event)

删除 event 事件与 callback 函数的绑定。此 callback 函数由 widgetcalss 类而来。