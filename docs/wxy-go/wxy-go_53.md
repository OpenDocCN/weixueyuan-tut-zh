# Go 语言 append()和 copy()：向切片追加元素和复制切片

> 原文：[`www.weixueyuan.net/a/565.html`](http://www.weixueyuan.net/a/565.html)

Go 语言中，函数 append() 用于往切片中追加新元素，可以向切片里面追加一个或者多个元素，也可以追加一个切片。append() 会改变切片所引用的数组的内容，从而影响到引用同一数组的其他切片。当使用 append() 追加元素到切片时，如果容量不够（也就是(cap-len) == 0），Go 就会创建一个新的内存地址来储存元素。

函数 copy() 会复制切片元素，将源切片中的元素复制到目标切片中，返回复制的元素的个数。copy() 方法不会建立源切片与目标切片之间的联系。也就是两个切片不存在联系，其中一个修改不影响另一个。

以上两个方法不适用于数组。

利用切片截取及 append() 函数实现切片删除元素。删除第一个元素，具体方法如下所示。

numbers = numbers[1:]

删除最后一个元素，具体方法如下所示。

numbers = numbers[:len(numbers)-1]

删除中间元素，具体方法如下所示。

a := int(len(numbers)/2)
fmt.Println(a)
numbers = append(numbers[:a], numbers[a+1:] ...)

接下来通过两个案例将 append() 和 copy() 进行对比，案例一代码如下所示。

```

package main
import "fmt"
func main(){
    fmt.Println("1\. --------------------")
    numbers := make([]int, 0, 20)
    printSlices("numbers:", numbers)
    numbers = append(numbers, 0)
    printSlices("numbers:", numbers)
    /*向切片添加一个元素*/
    numbers = append(numbers, 1)
    printSlices("numbers:", numbers)
    /*同时添加多个元素*/
    numbers = append(numbers, 2, 3, 4, 5, 6, 7)
    printSlices("numbers:", numbers)
    fmt.Println("2\. --------------------")
    //追加一个切片
    s1 := []int{100, 200, 300, 400, 500, 600, 700}
    numbers = append(numbers, s1...)
    printSlices("numbers:", numbers)
    fmt.Println("3\. -------------------")
    //切片删除元素
    //删除第一个元素
    numbers = numbers[1:]
    printSlices("numbers:", numbers)
    //删除最后一个元素
    numbers = numbers[:len(numbers)-1]
    printSlices("numbers:", numbers)
    //删除中间一个元素
    a := len(numbers)/2
    fmt.Println("中间数:", a)
    numbers = append(numbers[:a], numbers[a+1:]...)
    printSlices("numbers:", numbers)
    fmt.Println("4\. ==================")
    /*创建切片 numbers1 是之前切片的两倍容量*/
    numbers1 := make([]int, len(numbers), (cap(numbers))*2)
    /*复制 numbers 的内容到 numbers1*/
    count := copy(numbers1, numbers)
    fmt.Println("复制个数:", count)
    printSlices("numbers1:", numbers1)
    numbers[len(numbers)-1] = 99
    numbers1[0]= 100
    /*numbers1 与 numbers 两者不存在联系, numbers 发生变化时,
    numbers1 是不会随着变化的。也就是说 copy 方法是不会建立两个切片的联系的
    */
    printSlices("numbers1:", numbers1)
    printSlices("numbers:", numbers)
}
//输出切片格式化信息
func printSlices(name string, x []int){
    fmt.Print(name, "\t")
    fmt.Printf("addr:%p \t len=%d \t cap=%d \t slice=%v\n", x, len(x), cap(x), x)
}
```

运行结果如下：

1\. --------------------
numbers:    addr:0xc000092000    len=0   cap=20      slice=[]
numbers:    addr:0xc000092000    len=1   cap=20      slice=[0]
numbers:    addr:0xc000092000    len=2   cap=20      slice=[0 1]
numbers:    addr:0xc000092000    len=8   cap=20      slice=[0 1 2 3 4 5 6 7]
2\. --------------------
numbers:    addr:0xc000092000    len=15      cap=20      slice=[0 1 2 3 4 5 6 7 100 200 300 400 500 600 700]
3\. -------------------
numbers:    addr:0xc000092008    len=14      cap=19      slice=[1 2 3 4 5 6 7 100 200 300 400 500 600 700]
numbers:    addr:0xc000092008    len=13      cap=19      slice=[1 2 3 4 5 6 7 100 200 300 400 500 600]
中间数: 6
numbers:    addr:0xc000092008    len=12      cap=19      slice=[1 2 3 4 5 6 100 200 300 400 500 600]
4\. ==================
复制个数: 12
numbers1:   addr:0xc000094000    len=12      cap=38      slice=[1 2 3 4 5 6 100 200 300 400 500 600]
numbers1:   addr:0xc000094000    len=12      cap=38      slice=[100 2 3 4 5 6 100 200 300 400 500 600]
numbers:    addr:0xc000092008    len=12      cap=19      slice=[1 2 3 4 5 6 100 200 300 400 500 99]

案例二代码如下所示。

```

package main
import(
    "fmt"
    "strconv"
)
func main(){
    //思考:使用哪种初始化切片的方式更高效？
    var sa []string
    //sa := make([]string, 0, 20)
    printSliceMsg(sa)
    //当使用 append 追加元素到切片时, 如果容量不够, Go 就会创建一个新的切片变量来储存元素
    for i := 0; i < 15; i++ {
        sa = append(sa, strconv.Itoa(i))
        printSliceMsg(sa)
    }
    printSliceMsg(sa)
}
//打印输出格式化信息
func printSliceMsg(sa []string){
    fmt.Printf("addr:%p \t len:%v \t cap:%d \t value:%v \n", sa, len(sa), cap(sa), sa)
}
```

运行结果如下：

addr:0x0     len:0   cap:0   value:[]
addr:0xc0000421f0    len:1   cap:1   value:[0]
addr:0xc000004500    len:2   cap:2   value:[0 1]
addr:0xc0000180c0    len:3   cap:4   value:[0 1 2]
addr:0xc0000180c0    len:4   cap:4   value:[0 1 2 3]
addr:0xc000092000    len:5   cap:8   value:[0 1 2 3 4]
addr:0xc000092000    len:6   cap:8   value:[0 1 2 3 4 5]
addr:0xc000092000    len:7   cap:8   value:[0 1 2 3 4 5 6]
addr:0xc000092000    len:8   cap:8   value:[0 1 2 3 4 5 6 7]
addr:0xc000094000    len:9   cap:16      value:[0 1 2 3 4 5 6 7 8]
addr:0xc000094000    len:10      cap:16      value:[0 1 2 3 4 5 6 7 8 9]
addr:0xc000094000    len:11      cap:16      value:[0 1 2 3 4 5 6 7 8 9 10]
addr:0xc000094000    len:12      cap:16      value:[0 1 2 3 4 5 6 7 8 9 10 11]
addr:0xc000094000    len:13      cap:16      value:[0 1 2 3 4 5 6 7 8 9 10 11 12]
addr:0xc000094000    len:14      cap:16      value:[0 1 2 3 4 5 6 7 8 9 10 11 12 13]
addr:0xc000094000    len:15      cap:16      value:[0 1 2 3 4 5 6 7 8 9 10 11 12 13 14]
addr:0xc000094000    len:15      cap:16      value:[0 1 2 3 4 5 6 7 8 9 10 11 12 13 14]