# Go 语言 map 字典

> 原文：[`www.weixueyuan.net/a/569.html`](http://www.weixueyuan.net/a/569.html)

在现实生活中，通过字典可以查询某个词的语义，即词与语义建立了某种关系，通过词的索引便可以找到对应的语义。

Go 语言提供了内置类型 map，它将一个值与一个键关联起来，可以使用相应的键检索值。这种结构在其他资料中译成地图、映射或字典，但是在 Go 语言中习惯上翻译成集合。map 正如现实生活中的字典一样，使用词-语义进行数据的构建，其中词对应键（key），语义对应值（value），即键与值构成映射的关系，通常将两者称为键值对，这样通过键可以快速找到对应的值。

map 是一种集合，可以像遍历数组或切片那样去遍历它。因为 map 是由 Hash 表实现的，所以对 map 的读取顺序不固定。

map 是无序的，每次打印出来的 map 都会不一样，它不能通过 index 获取，而必须通过 key 获取。

map 的长度是不固定的，和切片一样可以扩展。内置的 len() 函数同样适用于 map，返回 map 拥有的键值对的数量。但是 map 不能通过 cap() 函数计算容量（或者说 cap() 函数的参数不可以是 map）。

同一个 map 中 key 必须保证唯一。key 的数据类型必须是可参与比较运算的类型，也就是支持 `==` 或 `!=` 操作的类型，如布尔型、整型、浮点型、字符串、数组。切片、函数等引用类型则不能作为 key 的数据类型。

map 的 value 可以是任何数据类型。map 和切片一样，也是一种引用类型。

## map 的语法

#### 1) map 的声明

可以使用 var 关键字来定义 map，也可以使用内建函数 make()。

使用 var 关键字定义 map 的语法格式如下。

var 变量名 map[key 类型] value 类型

使用 var 关键字声明 map，未初始化的 map 的默认值是 nil。nil map 不能存放键值对。如果要使用 map 存储键值对，必须在声明时初始化，或者使用 make() 函数分配到内存空间。

使用 make() 函数定义 map 的语法格式如下。

变量名 := make(map[key 类型]value 类型)

该声明方式，如果不初始化 map，map 也不等于 nil。

#### 2) map 的初始化赋值和遍历

map 的初始化赋值和遍历方式如下所示。

```

package main
import "fmt"
func main(){
    //1.声明时同时初始化
    var country = map[string]string{
        "China":    "Beijing",
        "Japan":    "Tokyo",
        "India":    "New Delhi",
        "France":   "Paris",
        "Italy":    "Rome",
    }
    fmt.Println(country)
    //短变量声明初始化方式
    rating := map[string]float64{"c":5, "Go":4.5, "Python":4.5, "C++":3}
    fmt.Println(rating)
    //2.创建 map 后再赋值
    countryMap := make(map[string]string)
    countryMap["China"]="Beijing"
    countryMap["Japan"]="Tokyo"
    countryMap["India"]="New Delhi"
    countryMap["France"]="Paris"
    countryMap["Italy"] ="Rome"
    //3\. 遍历 map(无序)
    //(1)key、value 都遍历
    for k, v := range countryMap{
        fmt.Println("国家", k, "首都", v)
    }
    fmt.Println("--------")
    //(2)只展示 value
    for _, v := range countryMap{
        fmt.Println("国家", "首都", v)
    }
    fmt.Println("--------")
    //(3)只展示 key
    for k := range countryMap{
        fmt.Println("国家", k, "首都", countryMap[k])
    }
}
```

运行结果如下：

map[China:Beijing France:Paris India:New Delhi Italy:Rome Japan:Tokyo]
map[C++:3 Go:4.5 Python:4.5 c:5]
国家 China 首都 Beijing
国家 Japan 首都 Tokyo
国家 India 首都 New Delhi
国家 France 首都 Paris
国家 Italy 首都 Rome
--------
国家 首都 Beijing
国家 首都 Tokyo
国家 首都 New Delhi
国家 首都 Paris
国家 首都 Rome
--------
国家 Italy 首都 Rome
国家 China 首都 Beijing
国家 Japan 首都 Tokyo
国家 India 首都 New Delhi
国家 France 首都 Paris

#### 3) 查看元素在集合中是否存在

可以通过 key 获取 map 中对应的 value 值。语法为：map[key]。当 key 不存在时，会得到该 value 值类型的默认值，比如 string 类型得到空字符串，int 类型得到 0，程序不会报错。所以可以通过 `value, ok := map[key]` 获知 key/value 是否存在。ok 是 bool 型，如果 ok 是 true，则该键值对存在，否则不存在。

【示例】查看元素集合。

```

package main
import "fmt"
func main(){
    countryMap := make(map[string]string)
    countryMap["China"]="Beijing"
    countryMap["Japan"]="Tokyo"
    countryMap["India"]="New Delhi"
    countryMap["France"]="paris"
    countryMap["Italy"]="Rome"
    //查看元素是否在 map 中存在
    value, ok := countryMap["England"]
    fmt.Printf("%q \n", value)
    fmt.Printf("%T, %v \n", ok, ok)
    if ok{
        fmt.Println("首都:", value)
    }else{
        fmt.Println("首都信息未检索到！")
    }
    //或者
    if value, ok := countryMap["USA"]; ok{
        fmt.Println("首都 :", value)
    }else{
        fmt.Println("首都信息未检索到！")
    }
}
```

运行结果如下所示：

""
bool, false
首都信息未检索到！
首都信息未检索到！