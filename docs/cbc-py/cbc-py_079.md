# Python 多态原理及实现（入门必读）

对于弱类型的语言来说，变量并没有声明类型，因此同一个变量完全可以在不同的时间引用不同的对象。当同一个变量在调用同一个方法时，完全可能呈现出多种行为（具体呈现出哪种行为由该变量所引用的对象来决定），这就是所谓的多态（Polymorphism）。

先看下面程序：

```
class Bird:
    def move(self, field):
        print('鸟在%s 上自由地飞翔' % field)
class Dog:
    def move(self, field):
        print('狗在%s 里飞快的奔跑' % field)
# x 变量被赋值为 Bird 对象
x = Bird()
# 调用 x 变量的 move()方法
x.move('天空')
# x 变量被赋值为 Dog 对象
x = Dog()
# 调用 x 变量的 move()方法
x.move('草地')
```

上面程序中 x 变量开始被赋值为 Bird 对象，因此当 x 变量执行 move() 方法时，它会表现出鸟类的飞翔行为。接下来 x 变量被赋值为 Dog 对象，因此当 x 变量执行 move() 方法时，它会表现出狗的奔跑行为。

运行上面程序，可以看到如下运行结果：

鸟在天空上自由地飞翔
狗在草地里飞快的奔跑

从上面的运行结果可以看出，同一个变量 x 在执行同一个 move() 方法时，由于 x 指向的对象不同，因此它呈现出不同的行为特征，这就是多态。

看到这里，可能有读者感到失望，这个多态有什么用啊？不就是创建对象、调用方法吗？看不出多态有什么优势啊？

实际上，多态是一种非常灵活的编程机制。假如我们要定义一个 Canvas（画布）类，这个画布类定义一个 draw_pic() 方法，该方法负责绘制各种图形。该 Canvas 类的代码如下：

```
class Canvas:
    def draw_pic(self, shape):
        print('--开始绘图--')
        shape.draw(self)
```

从上面代码可以看出，Canvas 的 draw_pic() 方法需要传入一个 shape 参数，该方法就是调用 shape 参数的 draw() 方法将自己绘制到画布上。

从上面程序来看，Canvas 的 draw_pic() 传入的参数对象只要带一个 draw() 方法就行，至于该方法具有何种行为（到底执行怎样的绘制行为），这与 draw_pic() 方法是完全分离的，这就为编程增加了很大的灵活性。下面程序定义了三个图形类，并为它们都提供了 draw() 方法，这样它们就能以不同的行为绘制在画布上，这就是多态的实际应用。看如下示例程序：

```
class Canvas:
    def draw_pic(self, shape):
        print('--开始绘图--')
        shape.draw(self)

class Rectangle:
    def draw(self, canvas):
        print('在%s 上绘制矩形' % canvas)
class Triangle:
    def draw(self, canvas):
        print('在%s 上绘制三角形' % canvas)
class Circle:
    def draw(self, canvas):
        print('在%s 上绘制圆形' % canvas)
c = Canvas()
# 传入 Rectangle 参数，绘制矩形
c.draw_pic(Rectangle())
# 传入 Triangle 参数，绘制三角形
c.draw_pic(Triangle())
# 传入 Circle 参数，绘制圆形
c.draw_pic(Circle())
```

运行上面代码， 可以看到如下输出结果：

--开始绘图--
在<__main__.Canvas object at 0x0000021CA36364A8>上绘制矩形
--开始绘图--
在<__main__.Canvas object at 0x0000021CA36364A8>上绘制三角形
--开始绘图--
在<__main__.Canvas object at 0x0000021CA36364A8>上绘制圆形

从上面这个例子可以体会到 Python 多态的优势。当程序涉及 Canvas 类的 draw_pic() 方法时，该方法所需的参数是非常灵活的，程序为该方法传入的参数对象只要具有指定方法就行，至于该方法呈现怎样的行为特征，则完全取决于对象本身，这大大提高了 draw_pic() 方法的灵活性。