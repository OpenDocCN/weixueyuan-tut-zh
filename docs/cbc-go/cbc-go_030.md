# Go 语言 append()为切片添加元素

Go 语言的内建函数 append() 可以为切片动态添加元素。每个切片会指向一片内存空间，这片空间能容纳一定数量的元素。当空间不能容纳足够多的元素时，切片就会进行“扩容”。“扩容”操作往往发生在 append() 函数调用时。

切片在扩容时，容量的扩展规律按容量的 2 倍数扩充，例如 1、2、4、8、16……，代码如下：

```
var numbers []int

for i := 0; i < 10; i++ {
    numbers = append(numbers, i)
    fmt.Printf("len: %d  cap: %d pointer: %p\n", len(numbers), cap(numbers), numbers)
}
```

代码输出如下：
len: 1  cap: 1 pointer: 0xc0420080e8
len: 2  cap: 2 pointer: 0xc042008150
len: 3  cap: 4 pointer: 0xc04200e320
len: 4  cap: 4 pointer: 0xc04200e320
len: 5  cap: 8 pointer: 0xc04200c200
len: 6  cap: 8 pointer: 0xc04200c200
len: 7  cap: 8 pointer: 0xc04200c200
len: 8  cap: 8 pointer: 0xc04200c200
len: 9  cap: 16 pointer: 0xc042074000
len: 10  cap: 16 pointer: 0xc042074000

代码说明如下：

*   第 1 行，声明一个整型切片。
*   第 4 行，循环向 numbers 切片添加 10 个数。
*   第 5 行中，打印输出切片的长度、容量和指针变化。使用 len() 函数查看切片拥有的元素个数，使用 cap() 函数查看切片的容量情况。

通过查看代码输出，有一个有意思的规律：len() 函数并不等于 cap。

往一个切片中不断添加元素的过程，类似于公司搬家。公司发展初期，资金紧张，人员很少，所以只需要很小的房间即可容纳所有的员工。随着业务的拓展和收入的增加就需要扩充工位，但是办公地的大小是固定的，无法改变。因此公司选择搬家，每次搬家就需要将所有的人员转移到新的办公点。

*   员工和工位就是切片中的元素。
*   办公地就是分配好的内存。
*   搬家就是重新分配内存。
*   无论搬多少次家，公司名称始终不会变，代表外部使用切片的变量名不会修改。
*   因为搬家后地址发生变化，因此内存“地址”也会有修改。

append() 函数除了添加一个元素外，也可以一次性添加很多元素。

```
var car []string

// 添加 1 个元素       
car = append(car, "OldDriver")

// 添加多个元素
car = append(car, "Ice", "Sniper", "Monk")

// 添加切片
team := []string{"Pig", "Flyingcake", "Chicken"}
car = append(car, team...)

fmt.Println(car)
```

代码输出如下：
[OldDriver Ice Sniper Monk Pig Flyingcake Chicken]

代码说明如下：

*   第 1 行，声明一个字符串切片。
*   第 4 行，往切片中添加一个元素。
*   第 7 行，使用 append() 函数向切片中添加多个元素。
*   第 10 行，声明另外一个字符串切片
*   第 11 行，在 team 后面加上了`...`，表示将 team 整个添加到 car 的后面。