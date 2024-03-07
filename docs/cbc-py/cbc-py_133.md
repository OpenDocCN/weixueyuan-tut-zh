# Python Tkinter Variable 类用法（附带实例分析）

Tkinter 支持将很多 GUI 组件与变量进行双向绑定，执行这种双向绑定后编程非常方便：

*   如果程序改变变量的值，GUI 组件的显示内容或值会随之改变。
*   当 GUI 组件的内容发生改变时（比如用户输入），变量的值也会随之改变。

为了让 Tkinter 组件与变量进行双向绑定，只要为这些组件指定 variable（通常绑定组件的 value）、textvariable（通常绑定组件显示的文本）等属性即可。

但这种双向绑定有一个限制，就是 Tkinter 不允许将组件和普通变量进行绑定，只能和 tkinter 包下 Variable 类的子类进行绑定。该类包含如下几个子类：

*   ShringVar()：用于包装 str 值的变量。
*   IntVar()：用于包装整型值的变量。
*   DoubleVar()：用于包装浮点值的变量。
*   BooleanVar()：用于包装 bool 值的变量。

对于 Variable 变量而言，如果要设置其保存的变量值，则使用它的 set() 方法；如果要得到其保存的变量值，则使用它的 get() 方法。

下面程序示范了将 Entry 组件与 StringVar 进行双向绑定，这样程序既可通过该 StringVar 改变 Entry 输入框显示的内容，也可通过该 StringVar 获取 Entry 输入框中的内容：

```
from tkinter import *
# 导入 ttk
from tkinter import ttk

class App:
    def __init__(self, master):
        self.master = master
        self.initWidgets()
    def initWidgets(self):
        self.st = StringVar()
        # 创建 Entry 组件，将其 textvariable 绑定到 self.st 变量
        ttk.Entry(self.master, textvariable=self.st,
            width=24,
            font=('StSong', 20, 'bold'),
            foreground='red').pack(fill=BOTH, expand=YES)
        # 创建 Frame 作为容器
        f = Frame(self.master)
        f.pack()
        # 创建两个按钮，将其放入 Frame 中
        ttk.Button(f, text='改变', command=self.change).pack(side=LEFT)
        ttk.Button(f, text='获取', command=self.get).pack(side=LEFT)
    def change(self):
        books = ('疯狂 Python 讲义', '疯狂 Kotlin 讲义', '疯狂 Swift 讲义')
        import random
        # 改变 self.st 变量的值，与之绑定的 Entry 的内容随之改变
        self.st.set(books[random.randint(0, 2)])
    def get(self):
        from tkinter import messagebox
        # 获取 self.st 变量的值，实际上就是获取与之绑定的 Entry 中的内容
        # 并使用消息框显示 self.st 变量的值
        messagebox.showinfo(title='输入内容', message=self.st.get() )
root = Tk()
root.title("variable 测试")
App(root)
root.mainloop()
```

上面程序中第 26 行代码调用 StringVar 改变变量的值，这样与该变量绑定的 Entry 中显示的内容会随之改变；第 31 行代码获取 StringVar 变量的值，实际上就是获取与该变量绑定的 Entry 中的内容。

运行该程序，单击界面上的“改变”按钮，将可以看到输入框中的内容会随之改变；如果单击界面上的“获取”按钮，将会看到程序弹出一个消息框，显示了用户在 Entry 输入框中输入的内容。