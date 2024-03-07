# Go 语言切片

> 原文：[`www.weixueyuan.net/a/558.html`](http://www.weixueyuan.net/a/558.html)

Go 语言中数组的长度不可改变，但在很多应用场景中，在初始定义数组时，数组的长度并不可预知，这样的序列集合无法满足要求。Go 语言中提供了另外一种内置类型“切片（slice）”，弥补了数组的缺陷而且切片的语法和数组很像。切片是可变长度的序列，序列中每个元素都是相同的类型。

从底层来看，切片引用了数组的对象。切片可以追加元素，在追加时可能使切片的容量增大。与数组相比，切片不需要设定长度，在 `[]` 中不用设定值，相对来说比较自由。

切片的数据结构可理解为一个结构体，这个结构体包含了三个元素。

*   指针：指向数组中切片指定的开始位置。
*   长度：即切片的长度。
*   容量：也就是切片开始位置到数组的最后位置的长度。

## 1、切片的语法

#### 1) 声明切片

声明一个未指定长度的数组来定义切片，具体示例如下。

var identifier []type

切片不需要说明长度。采用该声明方式且未初始化的切片为空切片。该切片默认为 nil，长度为 0。

使用 make() 函数来创建切片，语法格式如下。

var slice1 []type = make([]type, len)

使用 make() 函数来创建切片可以简写为如下格式。

slice1 := make([]type, len)

创建切片时可以指定容量，其中 capacity 为可选参数：make([]T, length, capacity)。详情如下所示。

```

package main
import "fmt"
func main() {
    var numbers = make([]int, 3, 5)
    fmt.Printf("%T\n", numbers)
    fmt.Printf("len=%d cap=%d slice=%v\n", len(numbers), cap(numbers), numbers)
}
```

运行结果如下：

[]int
len=3 cap=5 slice=[0 0 0]

#### 2) 初始化

直接初始化切片，语法格式如下。

s := [] int {1, 2, 3}

通过数组截取来初始化切片，语法格式如下。

arr := [5]int {1, 2, 3, 4, 5}
s := arr [:]

切片中包含数组所有元素，语法格式如下。

s := arr[startIndex:endIndex]

将 arr 中从下标 startIndex 到 endIndex-1 下的元素创建为一个新的切片（前闭后开），长度为 endIndex-startIndex。

缺省 endIndex 时表示一直到 arr 的最后一个元素，语法格式如下。

s := arr[startIndex:]

缺省 startIndex 时表示从 arr 的第一个元素开始，语法格式如下。

s := arr[:endIndex]

可以通过设置下限及上限来设置截取切片：[lower-bound:upper-bound]，如下所示。

```

package main
import "fmt"
func main(){
    /*创建切片*/
    numbers := []int{0, 1, 2, 3, 4, 5, 6, 7, 8}
    printSlice(numbers)
    /*打印原始切片*/
    fmt.Println("numbers ==", numbers)
    /*打印子切片从索引 1(包含)到索引 4(不包含)*/
    fmt.Println("numbers[1:4] ==", numbers[1:4])
    /*默认下限为 0*/
    fmt.Println("numbers[:3] ==", numbers[:3])
    /*默认上限为 len(s)*/
    fmt.Println("numbers[4:] ==", numbers[4:])
    /*打印子切片从索引 0(包含)到索引 2(不包含)*/
    number2 := numbers[:2]
    printSlice(number2)
    /*打印子切片从索引 2(包含)到索引 5(不包含)*/
    number3 := numbers[2:5]
    printSlice(number3)
}
func printSlice(x []int){
    fmt.Printf("len=%d cap=%d slice=%v \n", len(x), cap(x), x)
}
```

运行结果如下：

len=9 cap=9 slice=[0 1 2 3 4 5 6 7 8]
numbers == [0 1 2 3 4 5 6 7 8]
numbers[1:4] == [1 2 3]
numbers[:3] == [0 1 2]
numbers[4:] == [4 5 6 7 8]
len=2 cap=9 slice=[0 1]
len=3 cap=7 slice=[2 3 4]

## 2、切片是引用类型

切片没有自己的任何数据。它只是底层数组的一个引用。对切片所做的任何修改都将反映在底层数组中。数组是值类型，而切片是引用类型，两者的区别如下所示。

```

package main
import "fmt"
func main(){
    a := [4]float64{67.7, 89.8, 21, 78}
    b := []int{2, 3, 5}
    fmt.Printf("变量 a——地址：%p, 类型：%T, 数值：%v, 长度：%d \n", &a, a, a, len(a))
    fmt.Printf("变量 b——地址：%p, 类型：%T, 数值：%v, 长度：%d \n", &b, b, b, len(b))
    c := a
    d := b
    fmt.Printf("变量 c——地址：%p, 类型：%T, 数值：%v, 长度：%d \n", &c, c, c, len(c))
    fmt.Printf("变量 d——地址：%p, 类型：%T, 数值：%v, 长度：%d \n", &d, d, d, len(d))
    a[1] = 200
    fmt.Println("a=", a, "c=", c)
    d[0] = 100
    fmt.Println("b=", b, "d=", d)
}
```

运行结果如下：

变量 a——地址：0xc00000c360, 类型：[4]float64, 数值：[67.7 89.8 21 78], 长度：4
变量 b——地址：0xc0000044a0, 类型：[]int, 数值：[2 3 5], 长度：3
变量 c——地址：0xc00000c400, 类型：[4]float64, 数值：[67.7 89.8 21 78], 长度：4
变量 d——地址：0xc000004520, 类型：[]int, 数值：[2 3 5], 长度：3
a= [67.7 200 21 78] c= [67.7 89.8 21 78]
b= [100 3 5] d= [100 3 5]

修改切片数值，当多个切片共享相同的底层数组时，对每个元素所做的更改将在数组中反映出来。如下所示。

```

package main
import "fmt"
func main(){
    //定义数组
    arr := [3]int{1, 2, 3}
    //根据数组截取切片
    nums1 := arr[:]
    nums2 := arr[:]
    fmt.Println("arr=", arr)
    nums1[0] = 100
    fmt.Println("arr=", arr)
    nums2[1] = 200
    fmt.Println("arr=", arr)
}
```

运行结果如下：

arr= [1 2 3]
arr= [100 2 3]
arr= [100 200 3]