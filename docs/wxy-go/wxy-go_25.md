# Go 语言类型别名与类型定义

> 原文：[`www.weixueyuan.net/a/481.html`](http://www.weixueyuan.net/a/481.html)

类型别名是 Go1.9 版本添加的新功能。说到类型别名，无非是给类型名取一个有特殊含义的外号而已，就像武侠小说中的东邪西毒。假如在教室中，有两个同学叫张三，老师为了区分他们，通常会给他们起个别名：大张三、小张三。对于编程而言，类型别名主要用于解决兼容性的问题。

在 Go1.9 版本前内建类型定义的代码如下。

type byte uint8
type rune int32

而在 Go1.9 版本之后变更为如下代码。

type byte = uint8
type rune = int32

类型别名的语法格式如下。

type 类型别名 = 类型

定义类型的语法格式如下。

type 新的类型名 类型

具体的使用方法，如下所示。

type NewString string

该语句是将 NewString 定义为 string 类型。通过 type 关键字，NewString 会形成一种新的类型。NewString 本身依然具备 string 的特性。

type StringAlias = string

该语句是将 StringAlias 定义为 string 的一个别名。使用 StringAlias 与 string 等效。别名类型只会在代码中存在，编译完成时，不会有别名类型。

出于对程序性能的考虑，建议如下。

*   尽可能地使用 `:=` 去初始化声明一个变量（在函数内部）；
*   尽可能地使用字符代替字符串。