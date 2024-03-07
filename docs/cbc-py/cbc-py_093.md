# Python __dict__ 属性：查看对象内部所有属性名和属性值组成的字典

__dict__ 属性用于查看对象内部存储的所有属性名和属性值组成的字典，通常程序直接使用该属性即可。

程序使用 __dict__ 属性既可查看对象的所有内部状态，也可通过字典语法来访问或修改指定属性的值。例如如下程序：

```
class Item:
    def __init__ (self, name, price):
        self.name = name
        self.price = price
im = Item('鼠标', 28.9)
print(im.__dict__)  # ①
# 通过 __dict__ 访问 name 属性
print(im.__dict__['name'])
# 通过 __dict__ 访问 price 属性
print(im.__dict__['price'])
im.__dict__['name'] = '键盘'
im.__dict__['price'] = 32.8
print(im.name) # 键盘
print(im.price) # 32.8
```

上面程序中 ① 号代码直接输出对象的 dict 属性，这样将会直接输出该对象内部存储的所有属性名和属性值组成的 dict 对象；接下来的两行代码通过 dict 属性访问对象的 name、 price 两个属性；再后边两行代码通过 __dict__ 属性对 name、 price 两个属性赋值。

运行上面程序，可以看到如下输出结果：

{'name': '鼠标', 'price': 28.9}
鼠标
28.9
键盘
32.8