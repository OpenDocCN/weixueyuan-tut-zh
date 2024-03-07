# Go 语言函数参数的传递

> 原文：[`www.weixueyuan.net/a/548.html`](http://www.weixueyuan.net/a/548.html)

函数如果使用参数，该参数变量称为函数的形参。形参就像定义在函数体内的局部变量。调用函数，可以通过两种方式来传递参数，即值传递和引用传递，或者叫作传值和传引用。

## 1、值传递（传值）

值传递是指在调用函数时将实际参数复制一份传递到函数中，这样在函数中如果对参数进行修改，将不会影响到原内容数据。

默认情况下，Go 语言使用的是值传递，即在调用过程中不会影响到原内容数据。

每次调用函数，都将实参复制一份再传递到函数中。每次都复制一份，性能会下降，但是 Go 语言中使用指针和值传递配合就避免了性能降低问题，也就是通过传指针参数来解决实参复制的问题。

## 2、引用传递（传引用）

引用传递是指在调用函数时将实际参数的地址传递到函数中，那么在函数中对参数所进行的修改，将影响到原内容数据。

严格来说 Go 语言只有值传递这一种传参方式，Go 语言是没有引用传递的，但是 Go 语言中可以借助传指针来实现引用传递的效果。函数参数使用指针参数，传参的时候其实是复制一份指针参数，也就是复制了一份变量地址。

函数的参数如果是指针，当函数调用时，虽然参数仍然是按复制传递的，但是此时仅仅只是复制一个指针，也就是一个内存地址，这样就不用担心实参复制造成的内存浪费、时间开销、性能降低。

引用传递的作用如下：

*   传指针使得多个函数能操作同一个对象；
*   传指针更轻量级（8 bytes），只需要传内存地址。如果参数是非指针参数，那么值传递的过程中，每次在复制上面就会花费相对较多的系统开销（内存和时间）。所以要传递大的结构体的时候，用指针是一个明智的选择。

Go 语言中 slice、map、chan 类型的实现机制都类似指针，所以可以直接传递，而不必取地址后传递指针。

函数传 int 类型的值与引用的对比，如下所示。

```

package main
import "fmt"
func main(){
    a := 10
    fmt.Printf("1\. 变量 a 的内存地址：%p, 值为：%v \n\n", &a, a)
    fmt.Printf("========int 型变量 a 的内存地址：%p \n\n", a)
    changeIntVal(a)
    fmt.Printf("2\. changeIntVal 函数调用之后：变量 a 的内存地址：%p, 值为：%v \n\n", &a, a)
    changeIntPtr(&a)
    fmt.Printf("3\. changeIntPtr 函数调用之后：变量 a 的内存地址：%p, 值为：%v \n\n", &a, a)
}
func changeIntVal(a int){
    fmt.Printf("-------changeIntVal 函数内：值参数 a 的内存地址：%p, 值为：%v \n", &a, a)
    a = 90
}
func changeIntPtr(a *int){
    fmt.Printf("-------changeIntPtr 函数内：指针参数 a 的内存地址：%p, 值为：%v \n", &a, a)
    *a = 50
}
```

运行结果如下：

1\. 变量 a 的内存地址：0xc00000e0a8, 值为：10

========int 型变量 a 的内存地址：%!p(int=10)

-------changeIntVal 函数内：值参数 a 的内存地址：0xc00000e0e0, 值为：10
2\. changeIntVal 函数调用之后：变量 a 的内存地址：0xc00000e0a8, 值为：10

-------changeIntPtr 函数内：指针参数 a 的内存地址：0xc000006030, 值为：0xc00000e0a8
3\. changeIntPtr 函数调用之后：变量 a 的内存地址：0xc00000e0a8, 值为：50

在上面示例中，第 6 行打印的信息并不是内存地址，通过值传递的方式调用函数后变量 a 没有发生改变，通过引用传递的方式调用函数后变量 a 发生了变化，因此 int 是值类型。

函数传 slice 类型的值与引用的对比，如下所示。

```

package main
import "fmt"
func main(){
    a := []int{1, 2, 3, 4}
    fmt.Printf("1\. 变量 a 的内存地址是：%p, 值为：%v \n\n", &a, a) //[1, 2, 3, 4]
    fmt.Printf("切片型变量 a 内存地址是：%p \n\n", a) //可以获取到地址, 类似：0xc420018080
    //传值
    changeSliceVal(a)
    fmt.Printf("2\. changeSliceVal 函数调用后：变量 a 的内存地址是：%p, 值为：%v \n\n", &a, a)
    //传引用
    changeSlicePtr(&a)
    fmt.Printf("3\. changeSlicePtr 函数调用后：变量 a 的内存地址是：%p, 值为：%v \n\n", &a, a)
}
func changeSliceVal(a []int){
    fmt.Printf("-------changeSliceVal 函数内：值参数 a 的内存地址是：%p, 值为：%v \n", &a, a)
    fmt.Printf("-------changeSlicePtr 函数内：值参数 a 的内存地址是：%p \n", a)
    a[0] = 99
}
func changeSlicePtr(a *[]int){
    fmt.Printf("-------changeSlicePtr 函数内：指针参数 a 的内存地址是：%p, 值为：%v \n", &a, a)
    (*a)[1] = 250
}
```

运行结果如下：

1\. 变量 a 的内存地址是：0xc0000044a0, 值为：[1 2 3 4]

切片型变量 a 内存地址是：0xc00000c360

-------changeSliceVal 函数内：值参数 a 的内存地址是：0xc000004520, 值为：[1 2 3 4]
-------changeSlicePtr 函数内：值参数 a 的内存地址是：0xc00000c360
2\. changeSliceVal 函数调用后：变量 a 的内存地址是：0xc0000044a0, 值为：[99 2 3 4]

-------changeSlicePtr 函数内：指针参数 a 的内存地址是：0xc000006030, 值为：&[99 2 3 4]
3\. changeSlicePtr 函数调用后：变量 a 的内存地址是：0xc0000044a0, 值为：[99 250 3 4]

函数传数组，其类型的值与引用的对比，如下所示。

```

package main
import "fmt"
func main(){
    a := [4]int{1, 2, 3, 4}
    fmt.Printf("1\. 变量 a 的内存地址是：%p, 值为：%v \n\n", &a, a)
    fmt.Printf("数组型变量 a 内存地址是：%p \n\n", a)
    //传值
    changeArrayVal(a)
    fmt.Printf("2\. changeArrayVal 函数调用后：变量 a 的内存地址是：%p, 值为：%v \n\n", &a, a)
    //传引用
    changeArrayPtr(&a)
    fmt.Printf("3\. changeArrayPtr 函数调用后：变量 a 的内存地址是：%p, 值为：%v \n\n", &a, a)
}
func changeArrayVal(a [4]int){
    fmt.Printf("----------changeArrayVal 函数内：值参数 a 的内存地址是：%p, 值为：%v \n", &a, a)
    fmt.Printf("----------changeArrayPtr 函数内：值参数 a 的内存地址是：%p \n", a) //获取不到地址
    a[0] = 99
}
func changeArrayPtr(a *[4]int){
    fmt.Printf("----------changeArrayPtr 函数内：指针参数 a 的内存地址是：%p, 值为：%v \n", &a, a)
    (*a)[1] = 250
}
```

运行结果如下：

1\. 变量 a 的内存地址是：0xc00006c120, 值为：[1 2 3 4]

数组型变量 a 内存地址是：%!p([4]int=[1 2 3 4])

----------changeArrayVal 函数内：值参数 a 的内存地址是：0xc00006c180, 值为：[1 2 3 4]
----------changeArrayPtr 函数内：值参数 a 的内存地址是：%!p([4]int=[1 2 3 4])
2\. changeArrayVal 函数调用后：变量 a 的内存地址是：0xc00006c120, 值为：[1 2 3 4]

----------changeArrayPtr 函数内：指针参数 a 的内存地址是：0xc000098020, 值为：&[1 2 3 4]
3\. changeArrayPtr 函数调用后：变量 a 的内存地址是：0xc00006c120, 值为：[1 250 3 4]

函数传结构体，其类型的值与引用的对比，如下所示。

```

package main
import "fmt"
type Teacher struct{
    name string
    age int
    married bool
    sex int8
}
func main(){
    a := Teacher{"Steven", 35, true, 1}
    fmt.Printf("1\. 变量 a 的内存地址是：%p, 值为：%v \n\n", &a, a) //{Steven 35 true 1}
    fmt.Printf("struct 型变量 a 内存地址是：%p \n\n", a) //可以获取到地址？
    //传值
    changeStructVal(a)
    fmt.Printf("2\. changeArrayVal 函数调用后：变量 a 的内存地址是：%p, 值为：%v \n\n", &a, a)
    //传引用
    changeStructPtr(&a)
    fmt.Printf("3\. changeArrayPtr 函数调用后：变量 a 的内存地址是：%p, 值为：%v \n\n", &a, a)
}
func changeStructVal(a Teacher){
    fmt.Printf("---------changeArrayVal 函数内：值参数 a 的内存地址是：%p, 值为：%v \n", &a, a)
    fmt.Printf("---------changeArrayPtr 函数内：值参数 a 的内存地址是：%p \n", a) //获取不到地址？
    a.name = "Josh"
    a.age = 29
    a.married = false
}
func changeStructPtr(a *Teacher){
    fmt.Printf("-------changeArrayPtr 函数内：指针参数 a 的内存地址是：%p, 值为：%v \n", &a, a)
    (*a).name = "Daniel"
    (*a).age = 20
    (*a).married = false
}
```

运行结果如下：

1\. 变量 a 的内存地址是：0xc0000044a0, 值为：{Steven 35 true 1}

struct 型变量 a 内存地址是：%!p(main.Teacher={Steven 35 true 1})

---------changeArrayVal 函数内：值参数 a 的内存地址是：0xc000004520, 值为：{Steven 35 true 1}
---------changeArrayPtr 函数内：值参数 a 的内存地址是：%!p(main.Teacher={Steven 35 true 1})
2\. changeArrayVal 函数调用后：变量 a 的内存地址是：0xc0000044a0, 值为：{Steven 35 true 1}

-------changeArrayPtr 函数内：指针参数 a 的内存地址是：0xc000006030, 值为：&{Steven 35 true 1}
3\. changeArrayPtr 函数调用后：变量 a 的内存地址是：0xc0000044a0, 值为：{Daniel 20 false 1}

## 3、值传递和引用传递的细节问题

Go 语言中所有的传参都是值传递（传值），都是一个副本。副本的内容有的是值类型（int、string、bool、array、struct 属于值类型），这样在函数中就无法修改原内容数据；有的是引用类型（pointer、slice、map、chan 属于引用类型），这样就可以修改原内容数据。

是否可以修改原内容数据，和传值、传引用没有必然的关系。在 C++ 中，传引用肯定是可以修改原内容数据的；在 Go 语言里，虽然只有传值，但是也可以修改原内容数据，因为参数可以是引用类型。

传引用和引用类型是两个概念。虽然 Go 语言只有传值一种方式，但是可以通过传引用类型变量达到与传引用一样的效果。