# Go 语言 byte 和 rune：字符类型

> 原文：[`www.weixueyuan.net/a/473.html`](http://www.weixueyuan.net/a/473.html)

字符串中的每一个元素叫作“字符”，定义字符时使用单引号。Go 语言的字符有两种，如下表所示。

表：字符

| 类型 | 字节数 | 说明 |
| byte | 1 | 表示 UTF-8 字符串的单个字节的值，uint8 的别名类型 |
| rune | 4 | 表示单个 unicode 字符，int32 的别名类型 |

使用 fmt.Printf 中的 `%T` 动词可以输出变量的实际类型，使用这个方法可以查看 byte 和 rune 的本来类型，示例代码如下：

```

package main

import "fmt"

func main() {
    var a byte = 'a'
    fmt.Printf("%d %T\n", a, a)
    var b rune = '你'
    fmt.Printf("%d %T\n", b, b)
}
```

运行结果如下：

97 uint8
20320 int32

可以发现，byte 类型的变量 a，实际类型是 uint8，其值为 'a'，对应的 ASCII 编码为 97；rune 类型的变量 b 的实际类型是 int32，对应的 Unicode 码就是 20320。

Go 语言使用了特殊的 rune 类型来处理 Unicode，让基于 Unicode 的文本处理更为方便，也可以使用 byte 型进行默认字符串处理，性能和扩展性都有照顾。

Unicode 和 ASCII 都是一种字符集，字符集为每个字符分配一个唯一的 ID，我们使用到的所有字符在 Unicode 字符集中都有唯一的一个 ID 对应，例如上面例子中的 a 在 Unicode 与 ASCII 中的编码都是 97。“你”在 Unicode 中的编码为 20320，但是在不同国家的字符集中，“你”的 ID 会不同。而无论任何情况下，Unicode 中的字符的 ID 都是不会变化的。

UTF-8 是编码规则，将 Unicode 中字符的 ID 以某种方式进行编码。UTF-8 的是一种变长编码规则，从 1 到 4 个字节不等。编码规则如下：

*   0xxxxxx 表示文字符号 0～127，兼容 ASCII 字符集。
*   从 128 到 0x10ffff 表示其他字符。

根据这个规则，拉丁文语系的字符编码一般情况下，每个字符依然占用一个字节，而中文每个字符占用 3 个字节。