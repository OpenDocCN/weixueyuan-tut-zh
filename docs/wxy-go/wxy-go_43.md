# Go 语言可变参数

> 原文：[`www.weixueyuan.net/a/536.html`](http://www.weixueyuan.net/a/536.html)

如果一个函数的参数，类型一致，但个数不定，可以使用函数的可变参数。语法格式如下所示。

func 函数名(参数名 ...类型)[(返回值列表)]{
    // 函数体
}

该语法格式定义了一个接受任何数目、任何类型参数的函数。这里特殊的语法是三个点`...`，在一个变量后面加上三个点，表示从该处开始接受可变参数。

当要传递若干个值到可变参数函数中时，可以手动书写每个参数，也可以将一个 slice 传递给该函数，通过`...`可以将 slice 中的参数对应地传递给函数。

【示例】使用可变参数函数计算学员考试总成绩及平均成绩。

```

package main
import "fmt"
func main() {
    sum, avg, count := GetScore(90, 82.5, 73, 64.8)
    fmt.Printf("学员共有 %d 门成绩，总成绩为：%.2f，平均成绩为：%.2f", count, sum, avg)
    fmt.Println()
    scores := []float64{92, 72.5, 93, 74.5, 89, 87, 74}
    sum, avg, count = GetScore(scores...)
    fmt.Printf("学员共有 %d 门成绩，总成绩为：%.2f，平均成绩为：%.2f", count, sum, avg)
}
func GetScore(scores ...float64) (sum, avg float64, count int) {
    for _, value := range scores {
        sum += value
        count++
    }
    avg = sum / float64(count)
    return
}
```

运行结果如下：

学员共有 4 门成绩，总成绩为：310.30，平均成绩为：77.58
学员共有 7 门成绩，总成绩为：582.00，平均成绩为：83.14

使用可变参数应注意如下细节：

*   一个函数最多只能有一个可变参数；
*   若参数列表中还有其他类型参数，则可变参数写在所有参数的最后。