# Python super 函数：调用父类的构造方法

Python 的子类也会继承得到父类的构造方法，如果子类有多个直接父类，那么排在前面的父类的构造方法会被优先使用。例如如下代码：

```
class Employee :
    def __init__ (self, salary):
        self.salary = salary
    def work (self):
        print('普通员工正在写代码，工资是:', self.salary)
class Customer:
    def __init__ (self, favorite, address):
        self.favorite = favorite
        self.address = address
    def info (self):
        print('我是一个顾客，我的爱好是: %s,地址是%s' % (self.favorite, self.address))
# Manager 继承了 Employee、Customer
#class Manager (Employee, Customer):
class Manager (Customer, Employee):
    pass
#m = Manager(25000)
m = Manager('IT 产品', '广州')
#m.work()  #①
m.info()  #②
```

上面程序中第 13 行代码定义了 Manager 类，该类继承了 Employee 和 Customer 两个父类。接下来程序中的 Manager 类将会优先使用 Employee 类的构造方法（因为它排在前面），所以程序使用 Manager(25000）来创建 Manager 对象。该构造方法只会初始化 salary 实例变量，因此执行上面程序中 ① 号代码是没有任何问题的。

但是当执行到 ② 号代码时就会引发错误，这是由于程序在使用 Employee 类的构造方法创建 Manager 对象时，程序并未初始化 Customer 对象所需的两个实例变量：favorite 和 address，因此程序引发错误。

如果将程序中第 13 行代码改为如下形式：

class Manager (Customer, Employee)

上面 Manager 类将优先使用 Customer 类的构造方法，因此程序必须使用如下代码来创建 Manager 对象：

m = Manager('IT 产品','广州')

上面代码为 Manager 的构造方法传入两个参数，这明显是调用从 Customer 类继承得到的两个构造方法，此时程序将可以初始化 Customer 类中的 favorite 和 address 实例变量，但它又不能初始化 Employee 类中的 salary 实例变量。因此，此时程序中的 ② 号代码可以正常运行，但 ① 号代码会报错。

为了让 Manager 能同时初始化两个父类中的实例变量，Manager 应该定义自己的构造方法，即重写父类的构造方法。Python 要求：如果子类重写了父类的构造方法，那么子类的构造方法必须调用父类的构造方法。

子类的构造方法调用父类的构造方法有两种方式：

1.  使用未绑定方法，这种方式很容易理解。因为构造方法也是实例方法，当然可以通过这种方式来调用。
2.  使用 super() 函数调用父类的构造方法。

在交互式解释器中输入 help(super) 查看 super() 函数的帮助信息，可以看到如下输出信息：

class super(object)
|  super() -> same as super(__class__, <first argument>)
|  super(type) -> unbound super object
|  super(type, obj) -> bound super object; requires isinstance(obj, type)
|  super(type, type2) -> bound super object; requires issubclass(type2, type)
|  Typical use to call a cooperative superclass method:
|  class C(B):
|      def meth(self, arg):
|          super().meth(arg) #①
|  This works for class methods too:
|  class C(B):
|      @classmethod
|      def cmeth(cls, arg):
|          super().cmeth(arg) #②
...

从上面介绍可以看出，super 其实是一个类，因此调用 super() 的本质就是调用 super 类的构造方法来创建 super 对象。

从上面的帮助信息可以看到，使用 super() 构造方法最常用的做法就是不传入任何参数（这种做法与 super(type, obj) 的效果相同），然后通过 super 对象的方法既可调用父类的实例方法，也可调用父类的类方法。在调用父类的实例方法时，程序会完成第一个参数 self 的自动绑定，如上帮助信息中 ① 号信息所示。在调用类方法时，程序会完成第一个参数 cls 的自动绑定，如上面帮助信息中 ② 号信息所示。

掌握了 super() 函数的用法之后，接下来可以将上面程序改为如下形式：

```
# Manager 继承了 Employee、Customer
class Manager(Employee, Customer):
    # 重写父类的构造方法
    def __init__(self, salary, favorite, address):
        print('--Manager 的构造方法--')
        # 通过 super()函数调用父类的构造方法
        super().__init__(salary)
        # 与上一行代码的效果相同
        #super(Manager, self).__init__(salary)
        # 使用未绑定方法调用父类的构造方法
        Customer.__init__(self, favorite, address)
# 创建 Manager 对象
m = Manager(25000, 'IT 产品', '广州')
m.work()  #①
m.info()  #②
```

上面程序中，第 7、11 行代码分别示范了两种方式调用父类的构造方法。通过这种方式，Manager 类重写了父类的构造方法，并在构造方法中显式调用了父类的两个构造方法执行初始化，这样两个父类中的实例变量都能被初始化。

运行上面程序，可以看到如下运行结果：

--Manager 的构造方法--
普通员工正在写代码，工资是：2500。
我是一个顾客，我的爱好是：IT 产品，地址是广州

从上面的运行结果可以看到，此时程序中 ①、② 号代码都可正常运行，这正是程序对 Manager 重写的构造方法分别调用了两个父类的构造方法来完成初始化的结果。