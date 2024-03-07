# Python 类变量和实例变量（详解版）

在类体内定义的变量，默认属于类本身。如果把类当成类命名空间，那么该类变量其实就是定义在类命名空间内的变量，在类命名空间内定义的变量就属于类变量，Python 可以使用类来读取、修改类变量。

例如，下面代码定义了一个 Address 类，并为该类定义了多个类变量：

```
class Address :
    detail = '广州'
    post_code = '510660'
    def info (self):
        # 尝试直接访问类变量
        #print(detail) # 报错
        # 通过类来访问类变量
        print(Address.detail) # 输出 广州
        print(Address.post_code) # 输出 510660
# 通过类来访问 Address 类的类变量
print(Address.detail)
addr = Address()
addr.info()
# 修改 Address 类的类变量
Address.detail = '佛山'
Address.post_code = '460110'
addr.info()
```

该程序中，第二、三行代码为 Address 定义了两个类变量。

对于类变量而言，它们就是属于在类命名空间内定义的变量，因此程序不能直接访问这些变量，必须使用类名来调用类变量。不管是在全局范围内还是函数内访问这些类变量，都必须使用类名进行访问。

当程序第一次调用 Address 对象的 info() 方法输出两个类变量时，将会输出这两个类变量的初始值。接下来程序通过 Address 类修改了两个类变量的值，因此当程序第二次通过 info() 方法输出两个类变量时，将会输出这两个类变量修改之后的值。

运行上面代码，将会看到如下输出结果：

广州
广州
510660
佛山
460110

实际上，Python 完全允许使用对象来访问该对象所属类的类变量（当然还是推荐使用类访问类变量）。例如如下程序：

```
class Record:
    # 定义两个类变量
    item = '鼠标'
    date = '2016-06-16'
    def info (self):
        print('info 方法中: ', self.item)
        print('info 方法中: ', self.date)

rc = Record()
print(rc.item) # '鼠标'
print(rc.date) # '2016-06-16'
rc.info()
```

上面程序的 Record 中定义了两个类变量，接下来程序完全可以使用 Record 对象来访问这两个类变量。

在上面程序的 Record 类的 info() 方法中，程序使用 self 访问 Record 类的类变量，此时 self 代表 info() 方法的调用者，也就是 Record 对象，因此这是合法的。

在主程序代码区，程序创建了 Record 对象，并通过对象调用 Record 对象的 item、date 类变量，这也是合法的。

实际上，程序通过对象访问类变量，其本质还是通过类名在访问类变量。运行上面程序，将看到如下输出结果：

鼠标
2016-06-16
info 方法中:  鼠标
info 方法中:  2016-06-16

由于通过对象访问类变量的本质还是通过类名在访问，因此如果类变量发生了改变，当程序访问这些类变量时也会读到修改之后的值。例如为程序增加如下代码：

# 修改 Record 类的两个类变量
Record.item = '键盘'
Record.date = '2016-08-18'
# 调用 info()方法
rc.info()

上面程序修改了 Record 类的两个类变量，然后通过对象调用 info 实例方法。运行上面代码，将看到如下输出结果：

info 方法中:  键盘
info 方法中:  2016-08-18

从上面的输出结果可以看到，通过实例访问类变量的本质依然是通过类名在访问。

需要说明的是，Python 允许通过对象访问类变量，但如果程序通过对象尝试对类变量赋值，此时性质就变了，Python 是动态语言，赋值语句往往意味着定义新变量。

因此，如果程序通过对象对类变量赋值，其实不是对“类变量赋值”，而是定义新的实例变量。例如如下程序：

```
class Inventory:
    # 定义两个类变量
    item = '鼠标'
    quantity = 2000
    # 定义实例方法
    def change(self, item, quantity):
        # 下面赋值语句不是对类变量赋值，而是定义新的实例变量
        self.item = item
        self.quantity = quantity
# 创建 Inventory 对象
iv = Inventory()
iv.change('显示器', 500)
# 访问 iv 的 item 和 quantity 实例变量
print(iv.item) # 显示器
print(iv.quantity) # 500
# 访问 Inventory 的 item 和 quantity 类变量
print(Inventory.item) # 鼠标
print(Inventory.quantity) # 2000
```

上面程序中的第 8、9 行代码通过实例对 item、quantity 变量赋值，看上去很像是对类变量赋值，但实际上不是，而是重新定义了两个实例变量（如果第一次调用该方法）。

上面程序在调用 Inventory 对象的 change() 方法之后，访问 Inventory 对象的 item、quantity 变量，由于该对象本身己有这两个实例变量，因此程序将会输出该对象的实例变量的值；接下来程序通过 Inventory 访问它的 item、quantity 两个类变量，此时才是真的访问类变量。

运行上面程序，将看到如下输出结果：

显示器
500
鼠标
2000

如果程序通过类修改了两个类变量的值，程序中 Inventory 的实例变量的值也不会受到任何影响。例如如下代码：

```
Inventory.item = '类变量 item'
Inventory.quantity = '类变量 quantity'
# 访问 iv 的 item 和 quantity 实例变量
print(iv.item)
print(iv.quantity)
```

运行上面代码，可以看到如下输出结果：

显示器
500

上面程序开始就修改了 Inventory 类中两个类变量的值，但这种修改对 Inventory 对象的实例变量没有任何影响。

同样，如果程序对一个对象的实例变量进行了修改，这种修改也不会影响类变量和其他对象的实例变量。例如如下代码：

```
iv.item = '实例变量 item'
iv.quantity = '实例变量 quantity'
print(Inventory.item)
print(Inventory.quantity)
```

运行上面代码，将会看到如下输出结果：

类变量 item
类变量 quantity

从上面的输出结果来看，程序输出的依然是之前对类变量所赋的两个值。