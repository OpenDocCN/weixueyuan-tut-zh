# Go 语言 goto 语句——跳转到指定的标签

goto 语句通过标签进行代码间的无条件跳转。goto 语句可以在快速跳出循环、避免重复退出上有一定的帮助。Go 语言中使用 goto 语句能简化一些代码的实现过程。

## 使用 goto 退出多层循环

下面这段代码在满足条件时，需要连续退出两层循环，使用传统的编码方式如下：

```
package main

import "fmt"

func main() {

    var breakAgain bool

    // 外循环
    for x := 0; x < 10; x++ {

        // 内循环
        for y := 0; y < 10; y++ {

            // 满足某个条件时, 退出循环
            if y == 2 {

                // 设置退出标记
                breakAgain = true

                // 退出本次循环
                break
            }

        }

        // 根据标记, 还需要退出一次循环
        if breakAgain {
                break
        }

    }

    fmt.Println("done")
}
```

代码说明如下：

*   第 10 行，构建外循环。
*   第 13 行，构建内循环。
*   第 16 行，当 y==2 时需要退出所有的 for 循环。
*   第 19 行，默认情况下循环只能一层一层退出，为此就需要设置一个状态变量 isbreak，需要退出时，设置这个变量为 true。
*   第 22 行，使用 break 退出当前循环，执行后，代码调转到第 28 行。
*   第 28 行，退出一层循环后，根据 isbreak 变量判断是否需要再次退出外层循环。
*   第 34 行，退出所有循环后，打印 done。

## 使用 goto 集中处理错误

将上面的代码使用 Go 语言的 goto 语句进行优化。

goto 跳出循环：

```
package main

import "fmt"

func main() {

    for x := 0; x < 10; x++ {

        for y := 0; y < 10; y++ {

            if y == 2 {
                // 跳转到标签
                goto breakHere
            }

        }
    }

    // 手动返回, 避免执行进入标签
    return

    // 标签
breakHere:
    fmt.Println("done")
}
```

代码说明如下：

*   第 13 行，使用 goto 语句跳转到指明的标签处，标签在第 23 行定义。
*   第 20 行，标签只能被 goto 使用，但不影响代码执行流程，此处如果不手动返回，在不满足条件时，也会执行第 24 行代码。
*   第 23 行，定义 breakHere 标签。

使用 goto 语句后，无须额外的变量就可以快速退出所有的循环。

## 统一错误处理

多处错误处理存在代码重复时是非常棘手的，例如：

```
err := firstCheckError()
if err != nil {
    fmt.Println(err)
    exitProcess()
    return
}

err = secondCheckError()

if err != nil {
    fmt.Println(err)
    exitProcess()
    return
}

fmt.Println("done")
```

代码说明如下：

*   第 1 行，执行某逻辑，返回错误。
*   第 2～6 行，如果发生错误，打印错误退出进程。
*   第 8 行，执行某逻辑，返回错误。
*   第 10～14 行，发生错误后退出流程。
*   第 16 行，没有任何错误，打印完成。

在上面代码中，加粗部分都是重复的错误处理代码。后期陆续在这些代码中如果添加更多的判断，就需要在每一块雷同代码中依次修改，极易造成疏忽和错误。

如果使用 goto 语句来实现同样的逻辑：

```
    err := firstCheckError()
    if err != nil {
        goto onExit
    }

    err = secondCheckError()

    if err != nil {
        goto onExit
    }

    fmt.Println("done")

    return

onExit:
    fmt.Println(err)
    exitProcess()
```

代码说明如下：

*   第 3 行和第 9 行，发生错误时，跳转错误标签 onExit。
*   第 17 行和第 18 行，汇总所有流程进行错误打印并退出进程。