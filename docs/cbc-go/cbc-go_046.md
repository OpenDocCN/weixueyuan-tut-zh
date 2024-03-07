# Go 语言 continue（继续下一次循环）

continue 语句可以结束当前循环，开始下一次的循环迭代过程，仅限在 for 循环内使用。在 continue 语句后添加标签时，表示开始标签对应的循环。例如：

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
                continue OuterLoop
            }
        }
    }

}
```

代码输出结果如下：
0 2
1 2

代码说明：第 14 行将结束当前循环，开启下一次的外层循环，而不是第 10 行的循环。