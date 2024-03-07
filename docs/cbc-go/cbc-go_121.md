# go run 命令——编译并运行

Python 或者 Lua 语言可以在不输出二进制的情况下，将代码使用虚拟机直接执行。Go 语言虽然不使用虚拟机，但可使用 go run 指令达到同样的效果。

go run 命令会编译源码，并且直接执行源码的 main() 函数，不会在当前目录留下可执行文件。

下面我们准备一个 main.go 的文件来观察 go run 的运行结果，源码如下：

```
package main

import (   
    "fmt"
    "os"
)

func main() {

    fmt.Println("args:", os.Args)
}
```

这段代码的功能是将输入的参数打印出来。使用 go run 运行这个源码文件，命令如下：

$ go run main.go --filename xxx.go
args: [/tmp/go-build006874658/command-line-arguments/_obj/exe/main--filename xxx.go]

go run 不会在运行目录下生成任何文件，可执行文件被放在临时文件中被执行，工作目录被设置为当前目录。在 go run 的后部可以添加参数，这部分参数会作为代码可以接受的命令行输入提供给程序。

go run 不能使用“go run+包”的方式进行编译，如需快速编译运行包，需要使用如下步骤来代替：

1.  使用 go build 生成可执行文件。
2.  运行可执行文件。