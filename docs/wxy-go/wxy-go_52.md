# Go 语言 len()和 cap()：获取切片长度和容量

> 原文：[`www.weixueyuan.net/a/560.html`](http://www.weixueyuan.net/a/560.html)

切片的长度是切片中元素的数量。切片的容量是从创建切片的索引开始的底层数组中元素的数量。切片可以通过 len() 方法获取长度，可以通过 cap() 方法获取容量。数组计算 cap() 结果与 len() 相同，具体使用细节如下所示。

```

package main
import "fmt"
func main(){
    sliceCap()
}
func sliceCap(){
    arr0 := [...]string{"a", "b", "c", "d", "e", "f", "g", "h", "i", "j", "k"}
    fmt.Println("cap(arr0)=", cap(arr0), arr0)
    //截取数组, 形成切片
    s01 := arr0[2:8]
    fmt.Printf("%T\n", s01)
    fmt.Println("cap(s01)=", cap(s01), s01)
    s02 := arr0[4:7]
    fmt.Println("cap(s02)=", cap(s02), s02)
    //截取切片, 形成切片
    s03 := s01[3:9]
    fmt.Println("截取 s01[3:9]后形成 s03：", s03)
    s04 := s02[4:7]
    fmt.Println("截取 s02[4:7]后形成 s04：", s04)
    //切片是引用类型
    s04[0] = "x"
    fmt.Print(arr0, s01, s02, s03, s04)
}
```

运行结果如下：

cap(arr0)= 11 [a b c d e f g h i j k]
[]string
cap(s01)= 9 [c d e f g h]
cap(s02)= 7 [e f g]
截取 s01[3:9]后形成 s03： [f g h i j k]
截取 s02[4:7]后形成 s04： [i j k]
[a b c d e f g h x j k] [c d e f g h] [e f g] [f g h x j k] [x j k]