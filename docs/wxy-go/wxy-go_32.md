# Go 语言 if 条件判断语句

> 原文：[`www.weixueyuan.net/a/504.html`](http://www.weixueyuan.net/a/504.html)

Go 语言提供了以下几种条件判断语句，如下表所示。

表：条件判断语句

| 语句 | 描述 |
| if 语句 | if 语句由一个布尔表达式后紧跟一个或多个语句组成 |
| if...else 语句 | if 语句后可以使用可选的 else 语句，else 语句中的表达式在布尔表达式为 false 时执行 |
| if 嵌套语句 | 可以在 if 或 else if 语句中嵌入一个或多个 if 或 else if 语句 |

## 1、语法结构

Go 语言中 if 语句的语法如下所示。

if 布尔表达式 {
    /* 在布尔表达式为 true 时执行 */
}

if 在布尔表达式为 true 时，其后紧跟的语句块执行，如果为 false 则不执行。

Go 语言中 if...else 语句的语法如下所示。

if 布尔表达式 {
    /* 在布尔表达式为 true 时执行 */
} else {
    /* 在布尔表达式为 false 时执行 */
}

if 在布尔表达式为 true 时，其后紧跟的语句块执行，如果为 false 则执行 else 语句块。

Go 语言中 if...else if...else 语句的语法如下所示。

if 布尔表达式 {
    /* 在布尔表达式为 true 时执行 */
} else if 布尔表达式{
    /* 在布尔表达式为 true 时执行 */
...
} else {
    /* 在布尔表达式为 false 时执行 */
}

先判断 if 的布尔表达式，如果为 true，其后紧跟的语句块执行，如果为 false，再判断 else if 的布尔表达式，如果为 true，其后紧跟的语句块执行，如果为 false，再判断下一个 else if 的布尔表达式，以此类推，当最后一个 else if 的表达式为 false 时，执行 else 语句块。

在 if 语句的使用过程中，应注意以下细节。

*   不需使用括号将条件包含起来；
*   大括号 `{}` 必须存在，即使只有一行语句；
*   左括号必须在 if 或 else 的同一行；
*   在 if 之后，条件语句之前，可以添加变量初始化语句，使用分号`;`进行分隔。

【示例】使用 if...else 语句判断奇数偶数。

```

package main

import "fmt"

func main() {
    num := 20
    if num%2 == 0 {
        fmt.Println(num, "是偶数")
    } else {
        fmt.Println(num, "是奇数")
    }
}
```

运行结果如下：

20 是偶数

【示例】使用 if...else if...else 语句，判断学生的成绩。

```

package main

import "fmt"

func main() {
    score := 88
    if score >= 90 {
        fmt.Println("优秀")
    } else if score >= 80 {
        fmt.Println("良好")
    } else if score >= 70 {
        fmt.Println("中等")
    } else if score >= 60 {
        fmt.Println("及格")
    } else if score < 60 {
        fmt.Println("不及格")
    }
}
```

运行结果如下：

良好

## 2、if 语句的特殊写法

if 语句还有一个变体。它的语法如下所示。

if statement; condition {
    //代码块
}

还是以判断奇数偶数为例，示例代码如下所示。

```

package main

import "fmt"

func main() {
    if num := 10; num%2 == 0 {
        fmt.Println(num, "是偶数")
    } else {
        fmt.Println(num, "是奇数")
    }
}
```

运行结果如下：

10 是偶数

需要注意的是，变量 num 是定义在 if 里，所以 num 只能够在该 if...else 语句块中使用，否则编译器会报错。

## 3、if 嵌套语句

Go 语言中可以在 if 或 else if 语句中嵌入若干个 if 或 else if 语句，语法结构如下所示。

if 布尔表达式 1 {
    /* 在布尔表达式 1 为 true 时执行 */
    if 布尔表达式 2 {
        /* 在布尔表达式 2 为 true 时执行 */
    }
}

【示例】以嵌套的方式判断学生成绩。

```

package main

import "fmt"

func main() {
    if score := 98; score >= 60 {
        if score >= 70 {
            if score >= 80 {
                if score >= 90 {
                    fmt.Println("优秀")
                } else {
                    fmt.Println("良好")
                }
            } else {
                fmt.Println("中等")
            }
        } else {
            fmt.Println("及格")
        }
    } else {
        fmt.Println("不及格")
    }
}
```

运行结果如下：

优秀