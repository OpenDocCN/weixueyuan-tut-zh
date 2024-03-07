# Go 语言 for 循环语句

> 原文：[`www.weixueyuan.net/a/508.html`](http://www.weixueyuan.net/a/508.html)

在实际问题中，存在许多具有规律性的重复操作，因此在程序中需要重复执行某些语句。Go 语言提供了以下几种循环语句，如下表所示。

表：循环语句

| 循环类型 | 描述 |
| for 循环 | 重复执行语句块 |
| 循环嵌套 | 在 for 循环中嵌套一个或多个 for 循环 |

循环语句表示当条件满足时，可以反复地执行某段代码。for 是 Go 语言中唯一的循环语句，Go 语言没有 while、do...while 循环。按语法结构来分，Go 语言的 for 循环有 4 种形式，只有第一种使用分号。for 循环中 for 关键字后不能加小括号。

## 1、语法结构

#### 1) 语法结构一

for 关键字后有 3 个表达式，这是基本的 for 循环语法结构，如下所示。

for 初始语句 init; 条件表达式 condition; 结束语句 post {
    // 循环体代码
}

先执行初始语句，对控制变量赋初始值，初始语句只执行一次。

其次根据控制变量判断条件表达式的返回值，若其值为 true，满足循环条件，则执行循环体内语句，之后执行结束语句，开始下一次循环。

执行结束语句之后，将重新计算条件表达式的返回值，如果是 true，循环将继续执行，否则循环终止。然后执行循环体外语句。

【示例】使用 for 循环输出 0 到 10 的数字。

```

package main

import "fmt"

func main() {
    for i := 0; i <= 10; i++ {
        fmt.Printf("%d ", i)
    }
}
```

运行结果如下：

0 1 2 3 4 5 6 7 8 9 10

初始语句、条件表达式和结束语句 3 种组成部分都是可选的。因此这种基本的 for 循环语法结构又能演化出 4 种略有不同的写法。

初始语句是在第一次循环前执行的语句，一般为赋值表达式，给控制变量赋初始值。如果控制变量在此处被声明，其作用域将被局限在这个 for 的范围内。在 for 循环中声明的变量仅在循环范围内可用。初始语句可以省略不写，但是初始语句之后的分号必须要写。

省略初始语句的 for 循环写法如下所示。

```

package main

import "fmt"

func main() {
    i := 0
    for ; i <= 10; i++ {
        fmt.Printf("%d ", i)
    }
}
```

运行结果如下：

0 1 2 3 4 5 6 7 8 9 10

条件表达式（condition）是控制循环与否的开关，如果表达式为 true，则循环继续，否则结束循环。条件表达式可以省略不写，但之后的分号必须要写。省略条件表达式默认形成无限循环，示例代码如下所示：

```

package main

import "fmt"

func main() {
    i := 0
    for ; ; i++ {
        if i > 100 { // 使用 break 跳出循环
            break
        }
        fmt.Printf("%d ", i)
    }
}
```

运行上面的代码将输出 0 ~ 100 的数字。

结束语句（post），一般为赋值表达式，使控制变量递增或者递减。post 语句将在循环的每次成功迭代之后执行。

#### 2) 语法形式二

for 关键字后只有 1 个条件表达式，效果类似其他编程语言中的 while 循环。其语法结构如下所示。

for 循环条件 condition {
    // 循环体代码
}

示例代码如下所示：

```

package main

import "fmt"

func main() {
    var i int
    for i <= 10 {
        fmt.Printf("%d ", i)
        i++
    }
}
```

运行结果如下：

0 1 2 3 4 5 6 7 8 9 10

#### 3) 语法形式三

for 关键字后无表达式，效果与其他编程语言的 `for(;;) {}` 一致，此时 for 执行无限循环。其语法结构如下所示。

for {
    // 循环体代码
}

示例代码如下所示：

```

package main

import "fmt"

func main() {
    var i int
    for {
        if i > 10 {
            break
        }
        fmt.Printf("%d ", i)
        i++
    }
}
```

运行结果如下：

0 1 2 3 4 5 6 7 8 9 10

#### 4) 语法形式四（for...range）

for 循环的 range 格式对 string、slice、array、map、channel 等进行迭代循环。array、slice、string 返回索引和值；map 返回键和值；channel 只返回通道内的值。其语法结构如下所示。

for key, value := range oldMap {
    newMap[key] = value
}

示例代码如下所示：

```

package main

import "fmt"

func main() {
    str := "123ABCabc 一丁"
    for i, value := range str {
        fmt.Printf("第 %d 位的 ASCII 值为：%d，字符是 %c \n", i, value, value)
    }
}
```

运行结果如下：

第 0 位的 ASCII 值为：49，字符是 1
第 1 位的 ASCII 值为：50，字符是 2
第 2 位的 ASCII 值为：51，字符是 3
第 3 位的 ASCII 值为：65，字符是 A
第 4 位的 ASCII 值为：66，字符是 B
第 5 位的 ASCII 值为：67，字符是 C
第 6 位的 ASCII 值为：97，字符是 a
第 7 位的 ASCII 值为：98，字符是 b
第 8 位的 ASCII 值为：99，字符是 c
第 9 位的 ASCII 值为：19968，字符是 一
第 12 位的 ASCII 值为：19969，字符是 丁

【示例】使用 for 循环计算 1~40 所有 3 的倍数的和。

```

package main

import "fmt"

func main() {
    i := 1
    sum := 0
    for i <= 40 {
        if i%3 == 0 {
            sum += i
            fmt.Print(i)
            if i < 39 {
                fmt.Print("+")
            } else {
                fmt.Printf(" = %d \n", sum)
            }
        }
        i++
    }
}
```

运行结果如下：

3+6+9+12+15+18+21+24+27+30+33+36+39 = 273

## 2、for 嵌套循环语句

Go 语言允许在循环体内使用循环。其语法结构如下所示。

for [condition | (init; condition; increment) | Range] {
    for [condition | (init; condition; increment) | Range] {
        statement(s);
    }
    statement(s);
}

【示例】使用 for 循环打印直角三角形。

```

package main

import "fmt"

func main() {
    // 定义行数
    lines := 8
    for i := 0; i < lines; i++ {
        for n := 0; n < 2*i+1; n++ {
            fmt.Print("* ")
        }
        fmt.Println()
    }
}
```

运行结果如下：

*
* * *
* * * * *
* * * * * * *
* * * * * * * * *
* * * * * * * * * * *
* * * * * * * * * * * * *
* * * * * * * * * * * * * * *

【示例】打印九九乘法表。

```

package main

import "fmt"

func main() {
    for i := 1; i <= 9; i++ { // i 控制行数，是乘法中的第二个数
        for j := 1; j <= i; j++ { // j 控制每行的列数，是乘法中的第一个数
            fmt.Printf("%d*%d=%d ", j, i, i*j)
        }
        fmt.Println()
    }
}
```

运行结果如下：

1*1=1
1*2=2 2*2=4
1*3=3 2*3=6 3*3=9
1*4=4 2*4=8 3*4=12 4*4=16
1*5=5 2*5=10 3*5=15 4*5=20 5*5=25
1*6=6 2*6=12 3*6=18 4*6=24 5*6=30 6*6=36
1*7=7 2*7=14 3*7=21 4*7=28 5*7=35 6*7=42 7*7=49
1*8=8 2*8=16 3*8=24 4*8=32 5*8=40 6*8=48 7*8=56 8*8=64
1*9=9 2*9=18 3*9=27 4*9=36 5*9=45 6*9=54 7*9=63 8*9=72 9*9=81