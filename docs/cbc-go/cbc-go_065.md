# Go 语言构造函数

Go 语言的类型或结构体没有构造函数的功能。结构体的初始化过程可以使用函数封装实现。

其他编程语言构造函数的一些常见功能及特性如下：

*   每个类可以添加构造函数，多个构造函数使用函数重载实现。
*   构造函数一般与类名同名，且没有返回值。
*   构造函数有一个静态构造函数，一般用这个特性来调用父类的构造函数。
*   对于 C++ 来说，还有默认构造函数、拷贝构造函数等。

## 多种方式创建和初始化结构体——模拟构造函数重载

如果使用结构体描述猫的特性，那么根据猫的颜色和名字可以有不同种类的猫。那么不同的颜色和名字就是结构体的字段，同时可以使用颜色和名字构造不同种类的猫的实例，这个过程可以参考下面的代码：

```
type Cat struct {
    Color string
    Name  string
}

func NewCatByName(name string) *Cat {
    return &Cat{
        Name: name,
    }
}

func NewCatByColor(color string) *Cat {
    return &Cat{
        Color: color,
    }
}
```

代码说明如下：

*   第 1 行定义 Cat 结构，包含颜色和名字字段。
*   第 6 行定义用名字构造猫结构的函数，返回 Cat 指针。
*   第 7 行取地址实例化猫的结构体。
*   第 8 行初始化猫的名字字段，忽略颜色字段。
*   第 12 行定义用颜色构造猫结构的函数，返回 Cat 指针。

在这个例子中，颜色和名字两个属性的类型都是字符串。由于 Go 语言中没有函数重载，为了避免函数名字冲突，使用 NewCatByName() 和 NewCatByColor() 两个不同的函数名表示不同的 Cat 构造过程。

## 带有父子关系的结构体的构造和初始化——模拟父级构造调用

黑猫是一种猫，猫是黑猫的一种泛称。同时描述这两种概念时，就是派生，黑猫派生自猫的种类。使用结构体描述猫和黑猫的关系时，将猫（Cat）的结构体嵌入到黑猫（BlackCat）中，表示黑猫拥有猫的特性，然后再使用两个不同的构造函数分别构造出黑猫和猫两个结构体实例，参考下面的代码：

```
type Cat struct {
    Color string
    Name  string
}

type BlackCat struct {
    Cat  // 嵌入 Cat, 类似于派生
}

// “构造基类”
func NewCat(name string) *Cat {
    return &Cat{
        Name: name,
    }
}

// “构造子类”
func NewBlackCat(color string) *BlackCat {
    cat := &BlackCat{}
    cat.Color = color
    return cat
}
```

代码说明如下：

*   第 6 行，定义 BlackCat 结构，并嵌入了 Cat 结构体。BlackCat 拥有 Cat 的所有成员，实例化后可以自由访问 Cat 的所有成员。
*   第 11 行，NewCat() 函数定义了 Cat 的构造过程，使用名字作为参数，填充 Cat 结构体。
*   第 18 行，NewBlackCat() 使用 color 作为参数，构造返回 BlackCat 指针。
*   第 19 行，实例化 BlackCat 结构，此时 Cat 也同时被实例化。
*   第 20 行，填充 BlackCat 中嵌入的 Cat 颜色属性。BlackCat 没有任何成员，所有的成员都来自于 Cat。

这个例子中，Cat 结构体类似于面向对象中的“基类”。BlackCat 嵌入 Cat 结构体，类似于面向对象中的“派生”。实例化时，BlackCat 中的 Cat 也会一并被实例化。

总之，Go 语言中没有提供构造函数相关的特殊机制，用户根据自己的需求，将参数使用函数传递到结构体构造参数中即可完成构造函数的任务。