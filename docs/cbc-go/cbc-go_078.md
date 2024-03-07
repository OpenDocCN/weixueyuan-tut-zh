# Go 语言实现日志系统（支持多种输出方式）

日志可以用于查看和分析应用程序的运行状态。日志一般可以支持输出多种形式，如命令行、文件、网络等。

本例将搭建一个支持多种写入器的日志系统，可以自由扩展多种日志写入设备。

## 日志对外接口

本例中定义一个日志写入器接口（LogWriter），要求写入设备必须遵守这个接口协议才能被日志器（Logger）注册。日志器有一个写入器的注册方法（Logger 的 RegisterWriter() 方法）。

日志器还有一个 Log() 方法，进行日志的输出，这个函数会将日志写入到所有已经注册的日志写入器（LogWriter）中，详细代码实现请参考下面的代码。

```
package main

// 声明日志写入器接口
type LogWriter interface {
    Write(data interface{}) error
}

// 日志器
type Logger struct {
    // 这个日志器用到的日志写入器
    writerList []LogWriter
}

// 注册一个日志写入器
func (l *Logger) RegisterWriter(writer LogWriter) {
    l.writerList = append(l.writerList, writer)
}

// 将一个 data 类型的数据写入日志
func (l *Logger) Log(data interface{}) {

    // 遍历所有注册的写入器
    for _, writer := range l.writerList {

        // 将日志输出到每一个写入器中
        writer.Write(data)
    }
}

// 创建日志器的实例
func NewLogger() *Logger {
    return &Logger{}
}
```

代码说明如下：
第 4 行，声明日志写入器接口。这个接口可以被外部使用。日志的输出可以有多种设备，这个写入器就是用来实现一个日志的输出设备。
第 9 行，声明日志器结构。日志器使用 writeList 记录输出到哪些设备上。
第 15 行，使用日志器方法 RegisterWriter() 将一个日志写入器（LogWriter）注册到日志器（Logger）中。注册的意思就是将日志写入器的接口添加到 writeList 中。
第 20 行，日志器的 Log() 方法可以将 interface{} 类型的 data 写入到注册过的日志写入器中。
第 23 行，遍历日志器拥有的所有日志写入器。
第 26 行，将本次日志的内容写入日志写入器。
第 31 行，创建日志器的实例。

这个例子中，为了最大程度地展示接口的用法，仅仅只是将数据直接写入日志写入器中。复杂一些的日志器还可以将日期、级别等信息合并到数据中一并写入日志。

## 文件写入器

文件写入器（fileWriter）是众多日志写入器（LogWriter）中的一种。文件写入器的功能是根据一个文件名创建日志文件（fileWriter 的 SetFile 方法）。在有日志写入时，将日志写入文件中。

文件写入器代码：

```
package main

import (
    "errors"
    "fmt"
    "os"
)

// 声明文件写入器
type fileWriter struct {
    file *os.File
}

// 设置文件写入器写入的文件名
func (f *fileWriter) SetFile(filename string) (err error) {

    // 如果文件已经打开, 关闭前一个文件
    if f.file != nil {
        f.file.Close()
    }

    // 创建一个文件并保存文件句柄
    f.file, err = os.Create(filename)

    // 如果创建的过程出现错误, 则返回错误
    return err
}

// 实现 LogWriter 的 Write()方法
func (f *fileWriter) Write(data interface{}) error {

    // 日志文件可能没有创建成功
    if f.file == nil {

        // 日志文件没有准备好
        return errors.New("file not created")
    }

    // 将数据序列化为字符串
    str := fmt.Sprintf("%v\n", data)

    // 将数据以字节数组写入文件中
    _, err := f.file.Write([]byte(str))

    return err
}

// 创建文件写入器实例
func newFileWriter() *fileWriter {
    return &fileWriter{}
}
```

代码说明如下：

*   第 10 行，声明文件写入器，在结构体中保存一个文件句柄，以方便每次写入时操作。
*   第 15 行，文件写入器通过文件名创建文件，这里通过 SetFile 的参数提供一个文件名，并创建文件。
*   第 18 行，考虑到 SetFile() 方法可以被多次调用（函数可重入性），假设之前已经调用过 SetFile() 后再次调用，此时的 f.file 不为空，就需要关闭之前的文件，重新创建新的文件。
*   第 23 行，根据文件名创建文件，如果发生错误，通过 SetFile 的返回值返回。
*   第 30 行，fileWriter 的 Write() 方法实现了 LogWriter 接口的 Write() 方法。
*   第 33 行，如果文件没有准备好，文件句柄为 nil，此时使用 errors 包的 New() 函数返回一个错误对象，包含一个字符串“file not created”。
*   第 40 行，通过 Write() 方法传入的 data 参数是 interface{} 类型，而 f.file 的 Write() 方法需要的是 []byte 类型。使用 fmt.Sprintf 将 data 转换为字符串，这里使用的格式化参数是`%v`，意思是将 data 按其本来的值转换为字符串。
*   第 43 行，通过 f.file 的 Write() 方法，将 str 字符串转换为 []byte 字节数组，再写入到文件中。如果发生错误，则返回。

在操作文件时，会出现文件无法创建、无法写入等错误。开发中尽量不要忽略这些底层报出的错误，应该处理可能发生的所有错误。

文件使用完后，要注意使用 os.File 的 Close() 方法进行及时关闭，否则文件再次访问时会因为其属性出现无法读取、无法写入等错误。

#### 提示

一个完备的文件写入器会提供多种写入文件的模式，例子中使用的模式是将日志添加到日志文件的尾部。随着文件越来越大，文件的访问效率和查看便利性也会大大降低。此时，就需要另外一种写入模式：滚动写入文件。

滚动写入文件模式也是将日志添加到文件的尾部，但当文件达到设定的期望大小时，会自动开启一个新的文件继续写入文件，最终将获得多个日志文件。

日志文件名不仅可以按照文件大小进行分割，还可以按照日期范围进行分割。在到达设定的日期范围，如每天、每小时的周期范围时，日志器会自动创建新的日志文件。这种日志文件创建方法也能方便开发者按日志查看日志。

## 命令行写入器

在 UNIX 的思想中，一切皆文件。文件包括内存、磁盘、网络和命令行等。这种抽象方法方便我们访问这些看不见摸不着的虚拟资源。命令行在 Go 中也是一种文件，os.Stdout 对应标准输出，一般表示屏幕，也就是命令行，也可以被重定向为打印机或者磁盘文件；os.Stderr 对应标准错误输出，一般将错误输出到日志中，不过大多数情况，os.Stdout 会与 os.Stderr 合并输出；os.Stdin 对应标准输入，一般表示键盘。os.Stdout、os.Stderr、os.Stdin 都是 *os.File 类型，和文件一样实现了 io.Writer 接口的 Write() 方法。

下面的代码展示如何将命令行抽象为日志写入器：

```
package main

import (
    "fmt"
    "os"
)

// 命令行写入器
type consoleWriter struct {
}

// 实现 LogWriter 的 Write()方法
func (f *consoleWriter) Write(data interface{}) error {

    // 将数据序列化为字符串
    str := fmt.Sprintf("%v\n", data)

    // 将数据以字节数组写入命令行中
    _, err := os.Stdout.Write([]byte(str))

    return err
}

// 创建命令行写入器实例
func newConsoleWriter() *consoleWriter {
    return &consoleWriter{}
}
```

代码说明如下：

*   第 9 行，声明 consoleWriter 结构，以实现命令行写入器。
*   第 13 行，consoleWriter 的 Write() 方法实现了日志写入接口（LogWriter）的 Write() 方法。
*   第 16 行，与 fileWriter 类似，这里也将 data 通过 fmt.Sprintf 序列化为字符串。
*   第 19 行，与 fileWriter 类似，这里也将 str 字符串转换为字节数组并写入标准输出 os.Stdout。写入后的内容就会显示在命令行中。
*   第 25 行，创建命令行写入器的实例。

除了命令行写入器（consoleWriter）和文件写入器（fileWriter），读者还可以自行使用 net 包中的 Socket 封装实现网络写入器 socketWriter，让日志可以写入远程的服务器中或者可以跨进程进行日志保存和分析。

## 使用日志

在程序中使用日志器一般会先通过代码创建日志器（Logger），为日志器添加输出设备（fileWriter、consoleWriter 等）。这些设备中有一部分需要一些参数设定，如文件日志写入器需要提供文件名（fileWriter 的 SetFile() 方法）。

下面代码中展示了使用日志器的过程：

```
package main

import "fmt"

// 创建日志器
func createLogger() *Logger {

    // 创建日志器
    l := NewLogger()

    // 创建命令行写入器
    cw := newConsoleWriter()

    // 注册命令行写入器到日志器中
    l.RegisterWriter(cw)

    // 创建文件写入器
    fw := newFileWriter()

    // 设置文件名
    if err := fw.SetFile("log.log"); err != nil {
            fmt.Println(err)
    }

    // 注册文件写入器到日志器中
    l.RegisterWriter(fw)

    return l
}

func main() {

    // 准备日志器
    l := createLogger()

    // 写一个日志
    l.Log("hello")
}
```

代码说明如下：

*   第 6 行，一个创建日志的过程。这个过程一般隐藏在系统初始化中。程序启动时初始化一次。
*   第 9 行，创建一个日志器的实例，后面的代码会使用到它。
*   第 12 行，创建一个命令行写入器。如果全局有很多日志器，命令行写入器可以被共享，全局只会有一份。
*   第 18 行，创建一个文件写入器。一个程序的日志一般只有一个，因此不同的日志器也应该共享一个文件写入器。
*   第 21 行，创建好的文件写入器需要初始化写入的文件，通过文件名确定写入的文件。设置的过程可能会发生错误，发生错误时会输出错误信息。
*   第 26 行，将文件写入器注册到日志器中。
*   第 34 行，在程序一开始创建日志器。
*   第 37 行，往创建好的日志器中写入日志。

编译整个代码并运行，输出如下：
hello

同时，当前目录的 log.log 文件中也会出现 hello 字符。

#### 提示

Go 语言的 log 包实现了一个小型的日志系统。这个日志系统可以在创建日志器时选择输出设备、日志前缀及 flag，函数定义如下：

```
func New(out io.Writer, prefix string, flag int) *Logger {
    return &Logger{out: out, prefix: prefix, flag: flag}
}
```

在 flag 中，还可以定制日志中是否输出日期、日期精度和详细文件名等。

这个日志器在编写时，也最大程度地保证了输出的效率，如果读者对日志器的编写比较感兴趣，可以在 log 包的基础上进行扩展，形成方便自己使用的日志库。