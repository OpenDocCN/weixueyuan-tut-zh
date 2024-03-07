# Go 语言将秒转换为具体的时间

在本例中，使用一个数值表示时间中的“秒”值，然后使用 resolveTime() 函数将传入的秒数转换为天、小时和分钟等时间单位。

将秒解析为时间单位：

```
package main

import "fmt"

const (
    // 定义每分钟的秒数
    SecondsPerMinute = 60

    // 定义每小时的秒数
    SecondsPerHour = SecondsPerMinute * 60

    // 定义每天的秒数
    SecondsPerDay = SecondsPerHour * 24
)

// 将传入的“秒”解析为 3 种时间单位
func resolveTime(seconds int) (day int, hour int, minute int) {

    day = seconds / SecondsPerDay
    hour = seconds / SecondsPerHour
    minute = seconds / SecondsPerMinute

    return
}

func main() {

    // 将返回值作为打印参数
    fmt.Println(resolveTime(1000))

    // 只获取消息和分钟
    _, hour, minute := resolveTime(18000)
    fmt.Println(hour, minute)

    // 只获取天
    day, _, _ := resolveTime(90000)
    fmt.Println(day)
}
```

代码输出结果：
0 0 16
5 300
1

代码说明如下：

*   第 7 行，定义每分钟的秒数。
*   第 10 行，定义每小时的秒数，SecondsPerHour 常量值会在编译期间计算出结果。
*   第 13 行，定义每天的秒数。
*   第 17 行，定义 resolveTime() 函数，根据输入的秒数，返回 3 个整型值，含义分别是秒数对应的天数、小时数和分钟数（取整）。
*   第 29 行中，给定 1000 秒，对应是 16（16.6667 取整）分钟的秒数。resolveTime() 函数返回的 3 个变量会传递给 fmt.Println() 函数进行打印，因为 fmt.Println() 使用了可变参数，可以接收不定量的参数。
*   第 32 行，将 resolveTime() 函数中的 3 个返回值使用变量接收，但是第一个返回参数使用匿名函数接收，表示忽略这个变量。
*   第 36 行，忽略后两个返回值，只使用第一个返回值。