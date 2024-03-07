# Go 语言浮点类型（小数）

> 原文：[`www.weixueyuan.net/a/461.html`](http://www.weixueyuan.net/a/461.html)

浮点型表示存储的数据是实数，如 3.145。关于浮点型的说明，如下所示：

表：浮点型

| 类型 | 字节数 | 说明 |
| float32 | 4 | 32 位的浮点数 |
| float64 | 8  | 64 位的浮点数 |

浮点数的声明方式如下所示：

var x float32   // 声明 32 位浮点数
var y float64   // 声明 64 位浮点数

常量 math.MaxFloat32 表示 float32 能获取的最大值，大约是 3.4×10³⁸；常量 math.SmallestNonzeroFloat32 表示 float32 能获取的最小值，大约为 1.4×10^(-45)。

常量 math.MaxFloat64 表示 float64 能获取的最大值，大约是 1.8×10³⁰⁸；常量 math.SmallestNonzeroFloat64 表示 float64 能获取的最小值，大约为 4.9×10^(-324)。