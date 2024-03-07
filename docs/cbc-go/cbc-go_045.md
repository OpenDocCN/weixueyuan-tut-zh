# Go 语言 break（跳出循环）

break 语句可以结束 for、switch 和 select 的代码块。break 语句还可以在语句后面添加标签，表示退出某个标签对应的代码块，标签要求必须定义在对应的 for、switch 和 select 的代码块上。

跳出指定循环：

```
package main

import "fmt"

func main() {

OuterLoop:
    for i := 0; i < 2; i++ {
        for j := 0; j < 5; j++ {
            switch j {
            case 2:
                fmt.Println(i, j)
                break OuterLoop
            case 3:
                fmt.Println(i, j)
                break OuterLoop
            }
        }
    }
}
```

代码输出如下：
0 2

代码说明如下：

*   第 7 行，外层循环的标签。
*   第 8 行和第 9 行，双层循环。
*   第 10 行，使用 switch 进行数值分支判断。
*   第 13 和第 16 行，退出 OuterLoop 对应的循环之外，也就是跳转到第 20 行。