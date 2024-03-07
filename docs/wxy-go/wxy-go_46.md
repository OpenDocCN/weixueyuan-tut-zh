# Go 语言指针数组

> 原文：[`www.weixueyuan.net/a/541.html`](http://www.weixueyuan.net/a/541.html)

所谓指针数组就是元素为指针类型的数组，在 Go 语言中定义一个指针数组，语法格式如下所示。

var ptr [3]*string

有一个元素个数与之相同的数组，将该数组中每个元素的地址赋值给该指针数组。也就是说该指针数组与某一个数组完全对应。可以通过 `*` 指针变量获取到该地址所对应的数值。

下面通过一个示例演示指针数组的使用。

```

package main
import "fmt"
const COUNT int = 4
func main(){
    a := [COUNT]string{"abc", "ABC", "123", "一二三"}
    i := 0
    //定义指针数组
    var ptr [COUNT]*string
    fmt.Printf("%T, %v \n", ptr, ptr)
    for i = 0; i< COUNT; i++ {
        //将数组中每个元素的地址赋值给指针数组
        ptr[i] = &a[i]
    }
    fmt.Printf("%T, %v \n", ptr, ptr)
    //获取指针数组中第一个值, 其实就是一个地址
    fmt.Println(ptr[0])
    //根据数组元素的每个地址获取该地址所指向的元素的数值
    for i = 0; i< COUNT; i++ {
        fmt.Printf("a[%d] = %s \n", i, *ptr[i])
    }
}
```

运行结果如下：

[4]*string, [<nil> <nil> <nil> <nil>]
[4]*string, [0xc0000180c0 0xc0000180d0 0xc0000180e0 0xc0000180f0]
0xc0000180c0
a[0] = abc
a[1] = ABC
a[2] = 123
a[3] = 一二三