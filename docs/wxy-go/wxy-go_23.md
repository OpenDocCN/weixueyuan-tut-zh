# Go 语言数据类型转换

> 原文：[`www.weixueyuan.net/a/478.html`](http://www.weixueyuan.net/a/478.html)

Go 语言采用数据类型前置加括号的方式进行类型转换，格式如：T(表达式)。其中 T 表示要转换的类型；表达式则包括变量、数值、函数返回值等。

类型转换时，需要考虑两种类型之间的关系和范围，是否会发生数值截断。就像将 1000 毫升的水倒入容积为 500 毫升的瓶子里，余出来 500 毫升的水便会流失。值得注意的是，布尔型无法与其他类型进行转换，如下所示：

var a int = 100
b := float64(a) // 将 int 类型转换为 float64 类型
c := string(a)   // 将 int 类型转换为 string 类型

## 浮点型与整型之间的转换

float 和 int 的类型精度不同，使用时需要注意 float 转 int 时精度的损失。具体的使用方法如下所示：

```

package main

import "fmt"

func main() {
    chinese := 90
    english := 80.9
    avg := (chinese + int(english)) / 2
    avg2 := (float64(chinese) + english) / 2
    fmt.Printf("%T, %d\n", avg, avg)
    fmt.Printf("%T, %f\n", avg2, avg2)
}
```

运行结果如下：

int, 85
float64, 85.450000

## 整数类型转字符串类型

这种类型的转换，其实相当于 byte 或 rune 转 string。int 数值是 ASCII 码的编号或 unicode 字符集的编号，转成 string 就是根据字符集，将对应编号的字符查找出来。当该数值超出 unicode 编号范围，则转成的字符串显示为乱码。例如，19968 转 string，就是“一”。

备注：

*   ASCII 字符集中数字的十进制范围是 48～57；
*   ASCII 字符集中大写字母的十进制范围是 65～90；
*   ASCII 字符集中小写字母的十进制范围是 97～122；
*   unicode 字符集中汉字的范围是 4e00～9fa5，十进制范围是 19968～40869。

详情如下表所示。

表：ASCII 字符集

| ASCII 值 | 控制字符 | ASCII 值 | 控制字符 | ASCII 值 | 控制字符 | ASCII 值 | 控制字符 |
| 0 | NUT | 32 | (space) | 64 | @ | 96 | 、 |
| 1 | SOH | 33 | ! | 65 | A | 97 | a |
| 2 | STX | 34 | " | 66 | B | 98 | b |
| 3 | ETX | 35 | # | 67 | C | 99 | c |
| 4 | EOT | 36 | $ | 68 | D | 100 | d |
| 5 | ENQ | 37 | % | 69 | E | 101 | e |
| 6 | ACK | 38 | & | 70 | F | 102 | f |
| 7 | BEL | 39 | , | 71 | G | 103 | g |
| 8 | BS | 40 | ( | 72 | H | 104 | h |
| 9 | HT | 41 | ) | 73 | I | 105 | i |
| 10 | LF | 42 | * | 74 | J | 106 | j |
| 11 | VT | 43 | + | 75 | K | 107 | k |
| 12 | FF | 44 | , | 76 | L | 108 | l |
| 13 | CR | 45 | - | 77 | M | 109 | m |
| 14 | SO | 46 | . | 78 | N | 110 | n |
| 15 | SI | 47 | / | 79 | O | 111 | o |
| 16 | DLE | 48 | 0 | 80 | P | 112 | p |
| 17 | DCI | 49 | 1 | 81 | Q | 113 | q |
| 18 | DC2 | 50 | 2 | 82 | R | 114 | r |
| 19 | DC3 | 51 | 3 | 83 | S | 115 | s |
| 20 | DC4 | 52 | 4 | 84 | T | 116 | t |
| 21 | NAK | 53 | 5 | 85 | U | 117 | u |
| 22 | SYN | 54 | 6 | 86 | V | 118 | v |
| 23 | TB | 55 | 7 | 87 | W | 119 | w |
| 24 | CAN | 56 | 8 | 88 | X | 120 | x |
| 25 | EM | 57 | 9 | 89 | Y | 121 | y |
| 26 | SUB | 58 | : | 90 | Z | 122 | z |
| 27 | ESC | 59 | ; | 91 | [ | 123 | { |
| 28 | FS | 60 | < | 92 | / | 124 | &#124; |
| 29 | GS | 61 | = | 93 | ] | 125 | } |
| 30 | RS | 62 | > | 94 | ^ | 126 | ` |
| 31 | US | 63 | ? | 95 | _ | 127 | DEL |

具体的使用方法，如下所示：

```

package main

import "fmt"

func main() {
    a := 97
    x := 19968
    result := string(a)
    fmt.Println(result)
    result = string(x)
    fmt.Println(result)
}
```

运行结果如下：

a
一

注意：在 Go 语言中，不允许字符串转 int，否则会产生如下错误。

cannot convert str (type string) tc type int