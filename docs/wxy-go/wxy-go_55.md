# Go 语言 delete()函数：删除字典中的元素

> 原文：[`www.weixueyuan.net/a/571.html`](http://www.weixueyuan.net/a/571.html)

Go 语言的 delete(map, key) 函数用于删除集合的某个元素，参数为 map 和其对应的 key。删除函数不返回任何值。使用方式如下所示。

```

package main
import "fmt"
func main(){
    //1\. 声明并初始化一个 map
    map1 := map[string]string{
        "element":"div",
        "width":"100px",
        "height":"200px",
        "border":"solid",
        "background":"none",
    }
    //2\. 根据 key 删除 map 中的某个元素
    fmt.Println("删除前:", map1)
    if _, ok := map1["background"]; ok{
        delete(map1, "background")
    }
    fmt.Println("删除后:", map1)
    //3\. 清空 map
    //map1 = map[string]string{}
    map1 = make(map[string]string)
    fmt.Println("清空后:", map1)
}
```

运行结果如下：

删除前: map[background:none border:solid element:div height:200px width:100px]
删除后: map[border:solid element:div height:200px width:100px]
清空后: map[]

Go 语言没有为 map 提供清空所有元素的函数，清空 map 的唯一办法是重新 make 一个新的 map。不用担心垃圾回收的效率，Go 语言的垃圾回收比写一个清空函数更高效。