# Go 语言 copy()：切片复制（切片拷贝）

使用 Go 语言内建的 copy() 函数，可以迅速地将一个切片的数据复制到另外一个切片空间中，copy() 函数的使用格式如下：

copy( destSlice, srcSlice []T) int

*   srcSlice 为数据来源切片。
*   destSlice 为复制的目标。目标切片必须分配过空间且足够承载复制的元素个数。来源和目标的类型一致，copy 的返回值表示实际发生复制的元素个数。

下面的代码将演示对切片的引用和复制操作后对切片元素的影响。

```
package main

import "fmt"

func main() {

    // 设置元素数量为 1000
    const elementCount = 1000

    // 预分配足够多的元素切片
    srcData := make([]int, elementCount)

    // 将切片赋值
    for i := 0; i < elementCount; i++ {
        srcData[i] = i
    }

    // 引用切片数据
    refData := srcData

    // 预分配足够多的元素切片
    copyData := make([]int, elementCount)
    // 将数据复制到新的切片空间中
    copy(copyData, srcData)

    // 修改原始数据的第一个元素
    srcData[0] = 999

    // 打印引用切片的第一个元素
    fmt.Println(refData[0])

    // 打印复制切片的第一个和最后一个元素
    fmt.Println(copyData[0], copyData[elementCount-1])

    // 复制原始数据从 4 到 6(不包含)
    copy(copyData, srcData[4:6])

    for i := 0; i < 5; i++ {
        fmt.Printf("%d ", copyData[i])
    }
}
```

代码说明如下：

*   第 8 行，定义元素总量为 1000。
*   第 11 行，预分配拥有 1000 个元素的整型切片，这个切片将作为原始数据。
*   第 14～16 行，将 srcData 填充 0～999 的整型值。
*   第 19 行，将 refData 引用 srcData，切片不会因为等号操作进行元素的复制。
*   第 22 行，预分配与 srcData 等大（大小相等）、同类型的切片 copyData。
*   第 24 行，使用 copy() 函数将原始数据复制到 copyData 切片空间中。
*   第 27 行，修改原始数据的第一个元素为 999。
*   第 30 行，引用数据的第一个元素将会发生变化。
*   第 33 行，打印复制数据的首位数据，由于数据是复制的，因此不会发生变化。
*   第 36 行，将 srcData 的局部数据复制到 copyData 中。
*   第 38～40 行，打印复制局部数据后的 copyData 元素。